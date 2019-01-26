# Representing function properties in TypeScript

- date: 2017-04-11 13:00
- tags: typescript, javascript
- canonical_url: https://spin.atomicobject.com/2017/04/11/typescript-represent-function-properties/

----

iWe’ve been using [TypeScript](http://www.typescriptlang.org/) on an [Electron](https://electron.atom.io/) project. It’s been a huge win already—a little additional upfront investment gives us more confidence that our code is correct and reduces the chance that it will pass unexpectedly-shaped objects around, a source of many bugs in my past [Node](https://nodejs.org) applications.

But sometimes, it’s not immediately clear how to type certain kinds of objects. You can, of course, represent these as `any` whenever you need to—but any `any` you rely on can weaken your code’s quality. Last week, I discovered another way to avoid falling back on that crutch, thanks to the power of TypeScript’s type system.  

Electron applications rely on [IPC](https://electron.atom.io/docs/api/ipc-main/) to communicate between their main Node process and the renderer processes that present the user interface. Because our application uses IPC extensively, we decided to wrap Electron’s IPC libraries in a lightweight custom object that could emit log messages. This would allow us to trace IPC problems, and it could easily be replaced by a fake IPC implementation for unit testing.

To implement the logging of incoming IPC messages, we attached a wrapper function to Electron’s IPC library instead of the requested listener, like this:

```typescript
ipcMain.on(channel, (event: Electron.IpcMainEvent, ...args: any[]): void => {
  console.log(`heard ${channel}`, args);
  listener(event, ...args);
});
```

This worked great until we needed to implement one new piece of functionality: [removing a defunct listener](https://nodejs.org/api/events.html#events_emitter_removelistener_eventname_listener).

I’m Not Listening
-----------------

Removing a listener from an `EventEmitter` is important in a long-lived process, especially if you’re attaching listeners to a long-lived object like Electron’s IPC implementation.

If you fail to do this, you’ll not only be leaking memory by creating references that can’t be garbage-collected. You’ll also potentially be setting your application up for hard-to-trace bugs when zombie listeners you didn’t think were still around come roaring back to life.

If you’re simply listening to one event, solving this problem is fairly easy—just use [`.once`](https://nodejs.org/api/events.html#events_emitter_once_eventname_listener) instead of [`.on`](https://nodejs.org/api/events.html#events_emitter_on_eventname_listener), and the `EventEmitter` will take care of it for you.

If you’ve got multiple listeners, though—like a pair of success and error listeners, one of which must remove the other, you must use `.removeListener`—and that requires a function reference to identify which listener to remove. Because we wrapped the real listener, we need to ask the `EventEmitter` to remove our _wrapper_, which we don’t have a reference to—and tracking it is an exercise in complexity that I’d rather not add to a wrapper class.

The solution I arrived at involved attaching a `.wraps` property to our wrapper functions, holding a reference to the listener function:


```javascript
function wrapCallbackWithLogger(callback, message) {
  const listener = (event, ...args) => {
    console.log(message);
    callback(event, ...args);
  };
  listener.wraps = callback;
  return listener;
}
```

This allowed me to write code that would search the listeners attached to any particular IPC channel for the wrapper function wrapping the listener we were asked to remove:

```javascript
const listenerToRemove =
  listeners.filter(candidate => candidate.wraps === wrappedListener)[0];
```

Unfortunately, none of this made TypeScript very happy. And that is as it should be; `Function`s don’t have `wraps` properties!

Declaring Our Intent to Wrap
----------------------------

The very first thing I needed to do was declare some types so that TypeScript would understand the shape of our wrapper function. The function I wanted to wrap was easy enough; Electron types already had `IpcMainEventListener` and `IpcRendererEventListener` for both sides of its IPC implementation. I decided to write my own generic listener type:

```typescript
declare type IpcEventListener<E> = (event: E, ...args: any[]) => void;
```

Now that I had this type, I could extend it with the `.wraps` property easily:

```typescript
interface WrappedIpcEventListener<E> extends IpcEventListener<E> {
  wraps: IpcEventListener<E>;
}
```

Building the object was a bit trickier. In my original, TypeScript inferred `listener` as a basic callback for the IPC event listener, so it wouldn’t allow me to add the `wraps` property, and the basic callback didn’t satisfy `WrappedIpcEventListener`. The solution turned out to be doing it all in one step:

```typescript
function wrapCallbackWithLogger<E>(
  callback: (event: E, ...args: any[]) => void,
  message: string
): WrappedIpcEventListener<E> {
  return Object.assign(
    (event: E, ...args: any[]) => {
      console.log(message);
      callback(event, ...args);
    },
    {wraps: callback}
  );
}
```    

[`Object.assign`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) was the final ingredient to making the wrapping work—it took the wrapper callback and a new object containing just the `wraps` property. The result matched the `WrappedIpcEventListener` interface perfectly.

Making the filtering work required a little cast (as [the `listeners` method](https://nodejs.org/api/events.html#events_emitter_listeners_eventname) on `EventEmitter` returns `Array<Function>`), but I was comfortable with it. If a candidate function didn’t have a `wraps` property, it would return `undefined`, never matching the listener we want to remove:

```typescript
const listenerToRemove: WrappedIpcEventListener<E> =
  (listeners as Array<WrappedIpcEventListener<E>>)
    .filter(candidate => candidate.wraps === wrappedListener)[0];
```

With all this in place, the TypeScript compiler is happy, and we’re happy because we keep our extraordinarily useful IPC wrapper.

