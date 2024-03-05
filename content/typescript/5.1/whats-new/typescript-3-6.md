---
linkTitle: "TypeScript 3.6"
title: "TypeScript: Documentation - TypeScript 3.6"
description: "TypeScript 3.6 Release Notes"
weight: 16
type: docs
canonical: /typescript/5.2/whats-new/typescript-3-6
---

# TypeScript 3.6

## Stricter Generators {#stricter-generators}

TypeScript 3.6 introduces stricter checking for iterators and generator functions.
In earlier versions, users of generators had no way to differentiate whether a value was yielded or returned from a generator.

```ts
function* foo() {
if (Math.random() < 0.5) yield100;
return"Finished!";
}

letiter = foo();
letcurr = iter.next();
if (curr.done) {
// TypeScript 3.5 and prior thought this was a 'string | number'.
// It should know it's 'string' since 'done' was 'true'!
curr.value;
}
```

Additionally, generators just assumed the type of `yield` was always `any`.

```ts
function* bar() {
letx: { hello(): void } = yield;
x.hello();
}

letiter = bar();
iter.next();
iter.next(123); // oops! runtime error!
```

In TypeScript 3.6, the checker now knows that the correct type for `curr.value` should be `string` in our first example, and will correctly error on our call to `next()` in our last example.
This is thanks to some changes in the `Iterator` and `IteratorResult` type declarations to include a few new type parameters, and to a new type that TypeScript uses to represent generators called the `Generator` type.

The `Iterator` type now allows users to specify the yielded type, the returned type, and the type that `next` can accept.

```ts
interface Iterator<T, TReturn = any, TNext = undefined> {
// Takes either 0 or 1 arguments - doesn't accept 'undefined'
next(...args: [] | [TNext]): IteratorResult<T, TReturn>;
return?(value?: TReturn): IteratorResult<T, TReturn>;
throw?(e?: any): IteratorResult<T, TReturn>;
}
```

Building on that work, the new `Generator` type is an `Iterator` that always has both the `return` and `throw` methods present, and is also iterable.

```ts
interface Generator<T = unknown, TReturn = any, TNext = unknown>
extendsIterator<T, TReturn, TNext> {
next(...args: [] | [TNext]): IteratorResult<T, TReturn>;
return(value: TReturn): IteratorResult<T, TReturn>;
throw(e: any): IteratorResult<T, TReturn>;
  [Symbol.iterator ↗](https://www.typescriptlang.org): Generator<T, TReturn, TNext>;
}
```

To allow differentiation between returned values and yielded values, TypeScript 3.6 converts the `IteratorResult` type to a discriminated union type:

```ts
type IteratorResult<T, TReturn = any> =
  | IteratorYieldResult<T>
  | IteratorReturnResult<TReturn>;

interfaceIteratorYieldResult<TYield> {
done?: false;
value: TYield;
}

interfaceIteratorReturnResult<TReturn> {
done: true;
value: TReturn;
}
```

In short, what this means is that you’ll be able to appropriately narrow down values from iterators when dealing with them directly.

To correctly represent the types that can be passed in to a generator from calls to `next()`, TypeScript 3.6 also infers certain uses of `yield` within the body of a generator function.

```ts
function* foo() {
letx: string = yield;
console.log(x.toUpperCase());
}

letx = foo();
x.next(); // first call to 'next' is always ignored
x.next(42); // error! 'number' is not assignable to 'string'
```

If you’d prefer to be explicit, you can also enforce the type of values that can be returned, yielded, and evaluated from `yield` expressions using an explicit return type.
Below, `next()` can only be called with `boolean`s, and depending on the value of `done`, `value` is either a `string` or a `number`.

```ts
/**
 * - yields numbers
 * - returns strings
 * - can be passed in booleans
 */
function*counter(): Generator<number, string, boolean> {
leti = 0;
while (true) {
if (yieldi++) {
break;
    }
  }
return"done!";
}

variter = counter();
varcurr = iter.next();
while (!curr.done) {
console.log(curr.value);
curr = iter.next(curr.value === 5);
}
console.log(curr.value.toUpperCase());

// prints:
//
// 0
// 1
// 2
// 3
// 4
// 5
// DONE!
```

For more details on the change, [see the pull request here ↗](https://github.com/Microsoft/TypeScript/issues/2983).

## More Accurate Array Spread {#more-accurate-array-spread}

In pre-ES2015 targets, the most faithful emit for constructs like `for`/`of` loops and array spreads can be a bit heavy.
For this reason, TypeScript uses a simpler emit by default that only supports array types, and supports iterating on other types using the [`downlevelIteration` ↗](https://www.typescriptlang.org/tsconfig.html#downlevelIteration) flag.
The looser default without [`downlevelIteration` ↗](https://www.typescriptlang.org/tsconfig.html#downlevelIteration) works fairly well; however, there were some common cases where the transformation of array spreads had observable differences.
For example, the following array containing a spread

```ts
[...Array(5)];
```

can be rewritten as the following array literal

```js
[undefined, undefined, undefined, undefined, undefined];
```

However, TypeScript would instead transform the original code into this code:

```ts
Array(5).slice();
```

which is slightly different.
`Array(5)` produces an array with a length of 5, but with no defined property slots.

TypeScript 3.6 introduces a new `__spreadArrays` helper to accurately model what happens in ECMAScript 2015 in older targets outside of [`downlevelIteration` ↗](https://www.typescriptlang.org/tsconfig.html#downlevelIteration).
`__spreadArrays` is also available in [tslib ↗](https://github.com/Microsoft/tslib/).

For more information, [see the relevant pull request ↗](https://github.com/microsoft/TypeScript/pull/31166).

## Improved UX Around Promises {#improved-ux-around-promises}

TypeScript 3.6 introduces some improvements for when `Promise`s are mis-handled.

For example, it’s often very common to forget to `.then()` or `await` the contents of a `Promise` before passing it to another function.
TypeScript’s error messages are now specialized, and inform the user that perhaps they should consider using the `await` keyword.

```ts
interface User {
name: string;
age: number;
location: string;
}

declarefunctiongetUserData(): Promise<User>;
declarefunctiondisplayUser(user: User): void;

asyncfunctionf() {
displayUser(getUserData());
//              ~~~~~~~~~~~~~
// Argument of type 'Promise<User>' is not assignable to parameter of type 'User'.
//   ...
// Did you forget to use 'await'?
}
```

It’s also common to try to access a method before `await`-ing or `.then()`-ing a `Promise`.
This is another example, among many others, where we’re able to do better.

```ts
async function getCuteAnimals() {
fetch("https://reddit.com/r/aww.json").json();
//   ~~~~
// Property 'json' does not exist on type 'Promise<Response>'.
//
// Did you forget to use 'await'?
}
```

For more details, [see the originating issue ↗](https://github.com/microsoft/TypeScript/issues/30646), as well as the pull requests that link back to it.

## Better Unicode Support for Identifiers {#better-unicode-support-for-identifiers}

TypeScript 3.6 contains better support for Unicode characters in identifiers when emitting to ES2015 and later targets.

```ts
const 𝓱𝓮𝓵𝓵𝓸 = "world"; // previously disallowed, now allowed in '--target es2015'
```

## `import.meta` Support in SystemJS {#importmeta-support-in-systemjs}

TypeScript 3.6 supports transforming `import.meta` to `context.meta` when your `module` target is set to `system`.

```ts
// This module:

console.log(import.meta.url);

// gets turned into the following:

System.register([], function (exports, context) {
return {
setters: [],
execute:function () {
console.log(context.meta.url);
    },
  };
});
```

## `get` and `set` Accessors Are Allowed in Ambient Contexts {#get-and-set-accessors-are-allowed-in-ambient-contexts}

In previous versions of TypeScript, the language didn’t allow `get` and `set` accessors in ambient contexts (like in `declare`-d classes, or in `.d.ts` files in general).
The rationale was that accessors weren’t distinct from properties as far as writing and reading to these properties;
however, [because ECMAScript’s class fields proposal may have differing behavior from in existing versions of TypeScript ↗](https://github.com/tc39/proposal-class-fields/issues/248), we realized we needed a way to communicate this different behavior to provide appropriate errors in subclasses.

As a result, users can write getters and setters in ambient contexts in TypeScript 3.6.

```ts
declare class Foo {
// Allowed in 3.6+.
getx(): number;
setx(val: number);
}
```

In TypeScript 3.7, the compiler itself will take advantage of this feature so that generated `.d.ts` files will also emit `get`/`set` accessors.

## Ambient Classes and Functions Can Merge {#ambient-classes-and-functions-can-merge}

In previous versions of TypeScript, it was an error to merge classes and functions under any circumstances.
Now, ambient classes and functions (classes/functions with the `declare` modifier, or in `.d.ts` files) can merge.
This means that now you can write the following:

```ts
export declare function Point2D(x: number, y: number): Point2D;
exportdeclareclassPoint2D {
x: number;
y: number;
constructor(x: number, y: number);
}
```

instead of needing to use

```ts
export interface Point2D {
x: number;
y: number;
}
exportdeclarevarPoint2D: {
  (x: number, y: number): Point2D;
new (x: number, y: number): Point2D;
};
```

One advantage of this is that the callable constructor pattern can be easily expressed while also allowing namespaces to merge with these declarations (since `var` declarations can’t merge with `namespace`s).

In TypeScript 3.7, the compiler will take advantage of this feature so that `.d.ts` files generated from `.js` files can appropriately capture both the callability and constructability of a class-like function.

For more details, [see the original PR on GitHub ↗](https://github.com/microsoft/TypeScript/pull/32584).

## APIs to Support `--build` and `--incremental` {#apis-to-support---build-and---incremental}

TypeScript 3.0 introduced support for referencing other projects and building them incrementally using the `--build` flag.
Additionally, TypeScript 3.4 introduced the [`incremental` ↗](https://www.typescriptlang.org/tsconfig.html#incremental) flag for saving information about previous compilations to only rebuild certain files.
These flags were incredibly useful for structuring projects more flexibly and speeding builds up.
Unfortunately, using these flags didn’t work with 3rd party build tools like Gulp and Webpack.
TypeScript 3.6 now exposes two sets of APIs to operate on project references and incremental program building.

For creating [`incremental` ↗](https://www.typescriptlang.org/tsconfig.html#incremental) builds, users can leverage the `createIncrementalProgram` and `createIncrementalCompilerHost` APIs.
Users can also re-hydrate old program instances from `.tsbuildinfo` files generated by this API using the newly exposed `readBuilderProgram` function, which is only meant to be used as for creating new programs (i.e. you can’t modify the returned instance - it’s only meant to be used for the `oldProgram` parameter in other `create*Program` functions).

For leveraging project references, a new `createSolutionBuilder` function has been exposed, which returns an instance of the new type `SolutionBuilder`.

For more details on these APIs, you can [see the original pull request ↗](https://github.com/microsoft/TypeScript/pull/31432).

## Semicolon-Aware Code Edits {#semicolon-aware-code-edits}

Editors like Visual Studio and Visual Studio Code can automatically apply quick fixes, refactorings, and other transformations like automatically importing values from other modules.
These transformations are powered by TypeScript, and older versions of TypeScript unconditionally added semicolons to the end of every statement; unfortunately, this disagreed with many users’ style guidelines, and many users were displeased with the editor inserting semicolons.

TypeScript is now smart enough to detect whether your file uses semicolons when applying these sorts of edits.
If your file generally lacks semicolons, TypeScript won’t add one.

For more details, [see the corresponding pull request ↗](https://github.com/microsoft/TypeScript/pull/31801).

## Smarter Auto-Import Syntax {#smarter-auto-import-syntax}

JavaScript has a lot of different module syntaxes or conventions: the one in the ECMAScript standard, the one Node already supports (CommonJS), AMD, System.js, and more!
For the most part, TypeScript would default to auto-importing using ECMAScript module syntax, which was often inappropriate in certain TypeScript projects with different compiler settings, or in Node projects with plain JavaScript and `require` calls.

TypeScript 3.6 is now a bit smarter about looking at your existing imports before deciding on how to auto-import other modules.
You can [see more details in the original pull request here ↗](https://github.com/microsoft/TypeScript/pull/32684).

## New TypeScript Playground {#new-typescript-playground}

The TypeScript playground has received a much-needed refresh with handy new functionality!
The new playground is largely a fork of [Artem Tyurin ↗](https://github.com/agentcooper)’s [TypeScript playground ↗](https://github.com/agentcooper/typescript-play) which community members have been using more and more.
We owe Artem a big thanks for helping out here!

The new playground now supports many new options including:

- The [`target` ↗](https://www.typescriptlang.org/tsconfig.html#target) option (allowing users to switch out of `es5` to `es3`, `es2015`, `esnext`, etc.)
- All the strictness flags (including just [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict))
- Support for plain JavaScript files (using `allowJS` and optionally [`checkJs` ↗](https://www.typescriptlang.org/tsconfig.html#checkJs))

These options also persist when sharing links to playground samples, allowing users to more reliably share examples without having to tell the recipient “oh, don’t forget to turn on the [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny) option!“.

In the near future, we’re going to be refreshing the playground samples, adding JSX support, and polishing automatic type acquisition, meaning that you’ll be able to see the same experience on the playground as you’d get in your personal editor.

As we improve the playground and the website, [we welcome feedback and pull requests on GitHub ↗](https://github.com/microsoft/TypeScript-Website/)!
