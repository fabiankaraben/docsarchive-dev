---
linkTitle: "Iterators and Generators"
title: "TypeScript: Documentation - Iterators and Generators"
description: "How Iterators and Generators work in TypeScript"
weight: 6
type: docs
---

# Iterators and Generators

## Iterables {#iterables}

An object is deemed iterable if it has an implementation for the [`Symbol.iterator`](/typescript/5.2/reference/symbols#symboliterator) property.
Some built-in types like `Array`, `Map`, `Set`, `String`, `Int32Array`, `Uint32Array`, etc. have their `Symbol.iterator` property already implemented.
`Symbol.iterator` function on an object is responsible for returning the list of values to iterate on.

### `Iterable` interface {#iterable-interface}

`Iterable` is a type we can use if we want to take in types listed above which are iterable. Here is an example:

```ts
function toArray<X>(xs: Iterable<X>): X[] {
return [...xs]
}
```

### `for..of` statements {#forof-statements}

`for..of` loops over an iterable object, invoking the `Symbol.iterator` property on the object.
Here is a simple `for..of` loop on an array:

```ts
let someArray = [1, "string", false];

for (letentryofsomeArray) {
console.log(entry); // 1, "string", false
}
```

### `for..of` vs. `for..in` statements {#forof-vs-forin-statements}

Both `for..of` and `for..in` statements iterate over lists; the values iterated on are different though, `for..in` returns a list of *keys* on the object being iterated, whereas `for..of` returns a list of *values* of the numeric properties of the object being iterated.

Here is an example that demonstrates this distinction:

```ts
let list = [4, 5, 6];

for (letiinlist) {
console.log(i); // "0", "1", "2",
}

for (letioflist) {
console.log(i); // 4, 5, 6
}
```

Another distinction is that `for..in` operates on any object; it serves as a way to inspect properties on this object.
`for..of` on the other hand, is mainly interested in values of iterable objects. Built-in objects like `Map` and `Set` implement `Symbol.iterator` property allowing access to stored values.

```ts
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";

for (letpetinpets) {
console.log(pet); // "species"
}

for (letpetofpets) {
console.log(pet); // "Cat", "Dog", "Hamster"
}
```

### Code generation {#code-generation}

#### Targeting ES5 and ES3 {#targeting-es5-and-es3}

When targeting an ES5 or ES3-compliant engine, iterators are only allowed on values of `Array` type.
It is an error to use `for..of` loops on non-Array values, even if these non-Array values implement the `Symbol.iterator` property.

The compiler will generate a simple `for` loop for a `for..of` loop, for instance:

```ts
let numbers = [1, 2, 3];
for (letnumofnumbers) {
console.log(num);
}
```

will be generated as:

```js
var numbers = [1, 2, 3];
for (var_i = 0; _i < numbers.length; _i++) {
varnum = numbers[_i];
console.log(num);
}
```

#### Targeting ECMAScript 2015 and higher {#targeting-ecmascript-2015-and-higher}

When targeting an ECMAScript 2015-compliant engine, the compiler will generate `for..of` loops to target the built-in iterator implementation in the engine.
