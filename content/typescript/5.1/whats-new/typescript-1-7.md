---
linkTitle: "TypeScript 1.7"
title: "TypeScript: Documentation - TypeScript 1.7"
description: "TypeScript 1.7 Release Notes"
weight: 34
type: docs
---

# TypeScript 1.7

## `async`/`await` support in ES6 targets (Node v4+) {#asyncawait-support-in-es6-targets-node-v4}

TypeScript now supports asynchronous functions for engines that have native support for ES6 generators, e.g. Node v4 and above.
Asynchronous functions are prefixed with the `async` keyword;
`await` suspends the execution until an asynchronous function return promise is fulfilled and unwraps the value from the `Promise` returned.

##### Example {#example}

In the following example, each input element will be printed out one at a time with a 400ms delay:

```ts
"use strict";

// printDelayed is a 'Promise<void>'
asyncfunctionprintDelayed(elements: string[]) {
for (constelementofelements) {
awaitdelay(400);
console.log(element);
  }
}

asyncfunctiondelay(milliseconds: number) {
returnnewPromise<void>((resolve) => {
setTimeout(resolve, milliseconds);
  });
}

printDelayed(["Hello", "beautiful", "asynchronous", "world"]).then(() => {
console.log();
console.log("Printed every element!");
});
```

For more information see [async function reference ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) reference.

## Support for `--target ES6` with `--module` {#support-for---target-es6-with---module}

TypeScript 1.7 adds `ES6` to the list of options available for the [`module` ↗](https://www.typescriptlang.org/tsconfig.html#module) option and allows you to specify the module output when targeting `ES6`.
This provides more flexibility to target exactly the features you want in specific runtimes.

##### Example {#example-1}

```
{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[module ↗](https://www.typescriptlang.org/tsconfig.html#module)": "amd",
"[target ↗](https://www.typescriptlang.org/tsconfig.html#target)": "es6"
  }
}
```

## `this`-typing {#this-typing}

It is a common pattern to return the current object (i.e. `this`) from a method to create [fluent-style APIs ↗](https://wikipedia.org/wiki/Fluent_interface).
For instance, consider the following `BasicCalculator` module:

```ts
export default class BasicCalculator {
publicconstructor(protectedvalue: number = 0) {}

publiccurrentValue(): number {
returnthis.value;
  }

publicadd(operand: number) {
this.value += operand;
returnthis;
  }

publicsubtract(operand: number) {
this.value -= operand;
returnthis;
  }

publicmultiply(operand: number) {
this.value *= operand;
returnthis;
  }

publicdivide(operand: number) {
this.value /= operand;
returnthis;
  }
}
```

A user could express `2 * 5 + 1` as

```ts
import calc from "./BasicCalculator";

letv = newcalc(2).multiply(5).add(1).currentValue();
```

This often opens up very elegant ways of writing code; however, there was a problem for classes that wanted to extend from `BasicCalculator`.
Imagine a user wanted to start writing a `ScientificCalculator`:

```ts
import BasicCalculator from "./BasicCalculator";

exportdefaultclassScientificCalculatorextendsBasicCalculator {
publicconstructor(value = 0) {
super(value);
  }

publicsquare() {
this.value = this.value ** 2;
returnthis;
  }

publicsin() {
this.value = Math.sin(this.value);
returnthis;
  }
}
```

Because TypeScript used to infer the type `BasicCalculator` for each method in `BasicCalculator` that returned `this`, the type system would forget that it had `ScientificCalculator` whenever using a `BasicCalculator` method.

For instance:

```ts
import calc from "./ScientificCalculator";

letv = newcalc(0.5)
  .square()
  .divide(2)
  .sin() // Error: 'BasicCalculator' has no 'sin' method.
  .currentValue();
```

This is no longer the case - TypeScript now infers `this` to have a special type called `this` whenever inside an instance method of a class.
The `this` type is written as so, and basically means “the type of the left side of the dot in a method call”.

The `this` type is also useful with intersection types in describing libraries (e.g. Ember.js) that use mixin-style patterns to describe inheritance:

```ts
interface MyType {
extend<T>(other: T): this & T;
}
```

## ES7 exponentiation operator {#es7-exponentiation-operator}

TypeScript 1.7 supports upcoming [ES7/ES2016 exponentiation operators ↗](https://github.com/rwaldron/exponentiation-operator): `**` and `**=`.
The operators will be transformed in the output to ES3/ES5 using `Math.pow`.

##### Example {#example-2}

```ts
var x = 2 ** 3;
vary = 10;
y **= 2;
varz = -(4 ** 3);
```

Will generate the following JavaScript output:

```js
var x = Math.pow(2, 3);
vary = 10;
y = Math.pow(y, 2);
varz = -Math.pow(4, 3);
```

## Improved checking for destructuring object literal {#improved-checking-for-destructuring-object-literal}

TypeScript 1.7 makes checking of destructuring patterns with an object literal or array literal initializers less rigid and more intuitive.

When an object literal is contextually typed by the implied type of an object binding pattern:

- Properties with default values in the object binding pattern become optional in the object literal.
- Properties in the object binding pattern that have no match in the object literal are required to have a default value in the object binding pattern and are automatically added to the object literal type.
- Properties in the object literal that have no match in the object binding pattern are an error.

When an array literal is contextually typed by the implied type of an array binding pattern:

- Elements in the array binding pattern that have no match in the array literal are required to have a default value in the array binding pattern and are automatically added to the array literal type.

##### Example {#example-3}

```ts
// Type of f1 is (arg?: { x?: number, y?: number }) => void
functionf1({ x = 0, y = 0 } = {}) {}

// And can be called as:
f1();
f1({});
f1({ x:1 });
f1({ y:1 });
f1({ x:1, y:1 });

// Type of f2 is (arg?: (x: number, y?: number) => void
functionf2({ x, y = 0 } = { x:0 }) {}

f2();
f2({}); // Error, x not optional
f2({ x:1 });
f2({ y:1 }); // Error, x not optional
f2({ x:1, y:1 });
```

## Support for decorators when targeting ES3 {#support-for-decorators-when-targeting-es3}

Decorators are now allowed when targeting ES3.
TypeScript 1.7 removes the ES5-specific use of `reduceRight` from the `__decorate` helper.
The changes also inline calls `Object.getOwnPropertyDescriptor` and `Object.defineProperty` in a backwards-compatible fashion that allows for a to clean up the emit for ES5 and later by removing various repetitive calls to the aforementioned `Object` methods.
