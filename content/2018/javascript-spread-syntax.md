# Spreading the spread and rest love

- date: 2018-01-09 12:00
- tags: javascript
- canonical_url: https://spin.atomicobject.com/2018/01/09/javascript-spread-syntax/

----

JavaScript’s spread syntax has proven to be an extremely useful tool while working with immutable data structures as part of a React/Redux project.

Now that it’s widely available for objects in [LTS Node 8](https://nodejs.org/en/blog/release/v8.9.0) (as it has been for some time for other runtimes via [TypeScript](https://www.typescriptlang.org)), it’s interesting to go back and take a look at all it can do.

Object Spreads
--------------

In our codebase, object spreads get the most use by far. They look like this:

```javascript
const x = { a: 1, b: 2 };
const y = { ...x, c: 3 }; // y == {a: 1, b: 2, c: 3}
```

Using spread syntax, we expressed that `y`, a brand new object, should be composed of all of `x`’s properties and values, with `c` added to it. Most crucially, `x` is not modified at all—it is exactly the same object, untouched, as it always was.

Not modifying `x` satisfies a requirement for shallow immutability—that is, we know that if we keep a reference to `x`, it still has exactly the same property list that it always had, and none of its properties will point to any new objects. But we now also have `y`, which is `x`, but subtly changed.

It’s important to remember what shallow immutability _doesn’t_ give us, though. Notably, if any of `x`’s properties are mutable objects themselves, those objects can change on either `x` or its spread descendants, and the change will be visible across all of them. For this reason, it’s important to use object spreads on all the objects you’re modifying, like so:

```javascript
const x = {
  a: 1,
  b: {
    c: 2,
    d: 3
  }
};

const y = {
  ...x,
  b: {
    ...x.b,
    e: 4
  }
};

// y == { a: 1, b: { c: 2, d: 3, e: 4 } }
```

Of course, if you’re working on really deep objects, it’s a good idea to break up expressions like this into functions that can address the deeper parts of the object. You could also use a library like [lenses](https://github.com/atomicobject/lenses) to decouple the deep object knowledge from your implementation.

Destructuring Objects and the Rest Pattern
------------------------------------------

The complement to spreading objects into each other is using the _rest pattern_ in a destructuring assignment to pull selected things _out_ of an object in one assignment.

If you’re not familiar with a destructuring assignment, here’s one that pulls out properties from an object into separate variables:

```javascript
const x = { a: 1, b: 2, c: 3 };
const {a, b, c} = x;            // a == 1, b == 2, c == 3
```

When we bring the rest pattern into play, we can pull `a` out and create a new object to hold the rest of `x`:

```javascript
const x = { a: 1, b: 2, c: 3 };
const {a, ...y} = x;            // a == 1, y == { b: 2, c: 3 }
```

`y` is useful here because it is an immutably-derived version of `x` that is missing the `a` property. We don’t have to do anything with `a`; if we let it go out of scope and return `y`, we’ll be returning a new object that would represent what `x` would be with `a` deleted, except without mutating `x`.

You don’t need to use the name of the property for the variable you pull out, either. Just give the property a right-hand side, and whatever you name will spring into existence:

```javascript
const x = { a: 1, b: 2, c: 3 };
const {a: y, ...z} = x;         // y == 1, z == { b: 2, c: 3 }, a undefined
```

Array Spreads
-------------

Array spreads work very similarly to object spreads, but the place where you put the spread becomes more important.

```javascript
const x = [1, 2, 3];
const y = [ ...x, 4, 5, 6 ]; // y == [ 1, 2, 3, 4, 5, 6 ];
const z = [ 0, ...x, 4, 5 ]; // z == [ 0, 1, 2, 3, 4, 5 ];
```

The position of the spread determines where the spread array’s contents will appear in the new array. You can spread the contents of an array as many times as you need to, and anywhere:

```javascript
const x = [1, 2];
const y = [ 4, 5 ];
const z = [ 0, ...x, 3, ...y, 6 ]; // z = [ 0, 1, 2, 3, 4, 5, 6 ]
```

Just like array spreads, object spreads are shallow. The original array still points to the same things, and now the new array points to those same things. Any mutation of those things will be visible in both arrays.

Destructuring Arrays and the Rest Pattern
-----------------------------------------

Arrays can be destructured just like objects:

```javascript
const x = [ 1, 2 ];
const [ y, z ] = x; // y == 1, z == 2
```

We can use the rest pattern to pull out the rest of an array:

```javascript
const x = [ 1, 2, 3, 4, 5 ];
const [ y, ...z ] = x;       // y == 1, z == [ 2, 3, 4, 5 ]
```

We can’t, however, use the rest pattern quite as flexibly with arrays as we can with objects. A rest must be the _last_ part of a destructuring array assignment—so we can’t pull everything until the last element in an array, for example. If our needs are too complicated to use destructuring and the rest pattern, we’ll have to resort to [the `Array` API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array).

Function Call Spreads
---------------------

Function call spreads are a great way to pass an array of arguments to a function that expects each argument to be passed in separately:

```javascript
function x(a, b, c) {
  return a + b + c;
}

const y = [ 1, 2, 3 ];

x(...y); // returns 6
```

Much like array spreads, you can also use function call spreads positionally:

```javascript
function x(a, b, c) {
  return a + b + c;
}

const y = [ 2, 3 ];

x(1, ...y); // returns 6
```

This particular pattern gets the most use when you’re writing adapters that can work on many different kinds of functions. It allows you to save off a list of arguments and actually call the function later, without using [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply).

Rest Parameters
---------------

Just like rest, function call spreads are rest parameters, which let you collect a parameter list of arbitrary length without having to work with `arguments`. For example:

```javascript
function x(...y) {
  // for x(1, 2, 3), y is an array [ 1, 2, 3 ]
  // we'll use reduce to sum it
  return a.reduce((accumulator, value) => accumulator + value);
}

x(1, 2, 3);       // returns 6
x(1, 2, 3, 4, 5); // returns 15
```

Since you can use this as the inverse of spreading into a function call, you can use it in an adapter that can capture whatever arguments come in for later application.

But it’s less useful outside that sphere, in my opinion. While it might be tempting to make a function that can simply process an endless list of arguments (as above), it’s clearer to just pass an array in, with the understanding that the entire array will be processed.

One more thing: You can split your function parameters between defined and rest parameters, subject to the same restriction for arrays—the rest parameter must be the last one:

```javascript
function x(y, ...z) {
  return [y, z];
}

X(1, 2, 3); // returns [ 1, [ 2, 3 ] ]
```

Argument Destructuring
----------------------

Bringing it all together, there’s one more useful thing you can do with functions: use destructuring to pull arguments out of objects on the way in.

```javascript
function x({y, ...z}) {
  return [y, z];
}

x({ y: 1, z: 2, zz: 3 }); // returns [1, { z: 2, zz: 3 }]
```

Everything you’ve seen above for destructuring assignments works here, including array destructuring and the rest pattern. This can be pretty handy when you need to pull apart a tiny object. But beware, if you’re dealing with a large one, you may want to shift that destructure either into the interior of the function or forgo it entirely to avoid making your function header too dense.

Hopefully, you’ve found some useful new syntax to make your JavaScript code more readable and object manipulation more convenient.

_This article originally appeared [on Atomic Spin](https://https://spin.atomicobject.com/2018/01/09/javascript-spread-syntax/)._

