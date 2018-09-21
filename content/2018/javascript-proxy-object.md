# A JavaScript object that dynamically returns unknown properties 

- date: 2018-07-02 12:00
- tags: javascript
- canonical_url: https://spin.atomicobject.com/2018/07/02/javascript-proxy-object/

----

In our current project, we make extensive use of JavaScript objects as dictionaries, with the property name functioning as a key for the object we want to look up. We can use the `in` operator to test for property presence, and the dictionaries are perfectly JSON-serializable.

However, when it comes time to build test fixtures around these dictionaries for testing code that might look up lots of different keys, creating the test data for all of these keys becomes a large effort. Luckily, ES2015 has a solution.

The Old Way
-----------

Before I found this solution, I had code that looked like this:

```javascript
function generateValue(key) {
  return {data: key + '-data'}
}

export const FIXTURE = {
  a: generateValue('a'),
  b: generateValue('b'),
  c: generateValue('c'),
  d: {data: 'some-real-meaningful-data'}
};
```

This worked, but as I mentioned, we were looking at having to build out _lots_ of these generated values.

The New Way
-----------

Thankfully, [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) around a JavaScript object allows us to override key behavior, including property lookups and retrieval. It turns out to be really handy for this use case.

We can keep our `generateValue` function, so that we generate unique values for every key in the dictionary. We can also keep any non-generated values. Our new fixture code looks like this:

```javascript
export const FIXTURE = {
  d: {data: 'some-real-meaningful-data'}
};

export const MAGIC_FIXTURE = new Proxy(FIXTURE, {
  get: (target, prop) => prop in target ? target[prop] : generateValue(prop),
  has: (target, prop) => true
});
```

We’ve defined a new fixture, a `MAGIC_FIXTURE` that has special lookup behavior:

1.  For any property access, it will first check to see if the wrapped object has the requested property, and if so, return it. (This allows consumers to still access the fixed `d` property.) If it doesn’t exist, it generates and returns a new one on the fly.
2.  It claims to have any key requested. This allows consumers to do a check such as `'a' in MAGIC_FIXTURE`—a common pattern we use in assertions in our production code to catch invalid accesses.

While working with the Proxy object for this problem, I realized I could create a new kind of dictionary as well—one that would automatically assert that a requested key was present, throwing an `AssertionError` if it wasn’t there:

```javascript
const assert = require('assert');

function safeDictionary(dict) {
  return new Proxy(dict, {
    get: (target, prop) => {
      assert(prop in target, prop + ' key not found');
      return target[prop]
    }
  });
}
```

Proxy objects support lots of other behavior overrides as well, and they can be used on many things—not just basic objects like this.

Of course, you should be very careful using them. You can very easily cause unexpected behavior if you’re not careful to keep consuming code’s expectations met—but they can provide very powerful capabilities when passed into code you don’t control.

Happy Proxying!

_This article originally appeared [on Atomic Spin](https://spin.atomicobject.com/2018/07/02/javascript-proxy-object/)._

