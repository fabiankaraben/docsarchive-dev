---
linkTitle: "TypeScript 2.1"
title: "TypeScript: Documentation - TypeScript 2.1"
description: "TypeScript 2.1 Release Notes"
weight: 32
type: docs
---

# TypeScript 2.1

## `keyof` and Lookup Types {#keyof-and-lookup-types}

In JavaScript it is fairly common to have APIs that expect property names as parameters, but so far it hasn’t been possible to express the type relationships that occur in those APIs.

Enter Index Type Query or `keyof`;
An indexed type query `keyof T` yields the type of permitted property names for `T`.
A `keyof T` type is considered a subtype of `string`.

##### Example {#example}

```ts
interface Person {
name: string;
age: number;
location: string;
}

typeK1 = keyofPerson; // "name" | "age" | "location"
typeK2 = keyofPerson[]; // "length" | "push" | "pop" | "concat" | ...
typeK3 = keyof { [x: string]: Person }; // string
```

The dual of this is *indexed access types*, also called *lookup types*.
Syntactically, they look exactly like an element access, but are written as types:

##### Example {#example-1}

```ts
type P1 = Person["name"]; // string
typeP2 = Person["name" | "age"]; // string | number
typeP3 = string["charAt"]; // (pos: number) => string
typeP4 = string[]["push"]; // (...items: string[]) => number
typeP5 = string[][0]; // string
```

You can use this pattern with other parts of the type system to get type-safe lookups.

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
returnobj[key]; // Inferred type is T[K]
}

functionsetProperty<T, KextendskeyofT>(obj: T, key: K, value: T[K]) {
obj[key] = value;
}

letx = { foo:10, bar:"hello!" };

letfoo = getProperty(x, "foo"); // number
letbar = getProperty(x, "bar"); // string

letoops = getProperty(x, "wargarbl"); // Error! "wargarbl" is not "foo" | "bar"

setProperty(x, "foo", "string"); // Error!, string expected number
```

## Mapped Types {#mapped-types}

One common task is to take an existing type and make each of its properties entirely optional.
Let’s say we have a `Person`:

```ts
interface Person {
name: string;
age: number;
location: string;
}
```

A partial version of it would be:

```ts
interface PartialPerson {
name?: string;
age?: number;
location?: string;
}
```

with Mapped types, `PartialPerson` can be written as a generalized transformation on the type `Person` as:

```ts
type Partial<T> = {
  [PinkeyofT]?: T[P];
};

typePartialPerson = Partial<Person>;
```

Mapped types are produced by taking a union of literal types, and computing a set of properties for a new object type.
They’re like [list comprehensions in Python ↗](https://docs.python.org/2/tutorial/datastructures.html#nested-list-comprehensions), but instead of producing new elements in a list, they produce new properties in a type.

In addition to `Partial`, Mapped Types can express many useful transformations on types:

```ts
// Keep types the same, but make each property to be read-only.
typeReadonly<T> = {
readonly [PinkeyofT]: T[P];
};

// Same property names, but make the value a promise instead of a concrete one
typeDeferred<T> = {
  [PinkeyofT]: Promise<T[P]>;
};

// Wrap proxies around properties of T
typeProxify<T> = {
  [PinkeyofT]: { get(): T[P]; set(v: T[P]): void };
};
```

## `Partial`, `Readonly`, `Record`, and `Pick` {#partial-readonly-record-and-pick}

`Partial` and `Readonly`, as described earlier, are very useful constructs.
You can use them to describe some common JS routines like:

```ts
function assign<T>(obj: T, props: Partial<T>): void;
functionfreeze<T>(obj: T): Readonly<T>;
```

Because of that, they are now included by default in the standard library.

We’re also including two other utility types as well: `Record` and `Pick`.

```ts
// From T pick a set of properties K
declarefunctionpick<T, KextendskeyofT>(obj: T, ...keys: K[]): Pick<T, K>;

constnameAndAgeOnly = pick(person, "name", "age"); // { name: string, age: number }
```

```ts
// For every properties K of type T, transform it to U
functionmapObject<Kextendsstring, T, U>(
obj: Record<K, T>,
f: (x: T) =>U
): Record<K, U>;

constnames = { foo:"hello", bar:"world", baz:"bye" };
constlengths = mapObject(names, s=>s.length); // { foo: number, bar: number, baz: number }
```

## Object Spread and Rest {#object-spread-and-rest}

TypeScript 2.1 brings support for [ESnext Spread and Rest ↗](https://github.com/sebmarkbage/ecmascript-rest-spread).

Similar to array spread, spreading an object can be handy to get a shallow copy:

```ts
let copy = { ...original };
```

Similarly, you can merge several different objects.
In the following example, `merged` will have properties from `foo`, `bar`, and `baz`.

```ts
let merged = { ...foo, ...bar, ...baz };
```

You can also override existing properties and add new ones:

```ts
let obj = { x: 1, y: "string" };
varnewObj = { ...obj, z:3, y:4 }; // { x: number, y: number, z: number }
```

The order of specifying spread operations determines what properties end up in the resulting object;
properties in later spreads “win out” over previously created properties.

Object rests are the dual of object spreads, in that they can extract any extra properties that don’t get picked up when destructuring an element:

```ts
let obj = { x: 1, y: 1, z: 1 };
let { z, ...obj1 } = obj;
obj1; // {x: number, y:number};
```

## Downlevel Async Functions {#downlevel-async-functions}

This feature was supported before TypeScript 2.1, but only when targeting ES6/ES2015.
TypeScript 2.1 brings the capability to ES3 and ES5 run-times, meaning you’ll be free to take advantage of it no matter what environment you’re using.

> Note: first, we need to make sure our run-time has an ECMAScript-compliant `Promise` available globally.
> That might involve grabbing [a polyfill ↗](https://github.com/stefanpenner/es6-promise) for `Promise`, or relying on one that you might have in the run-time that you’re targeting.
> We also need to make sure that TypeScript knows `Promise` exists by setting your [`lib` ↗](https://www.typescriptlang.org/tsconfig.html#lib) option to something like `"dom", "es2015"` or `"dom", "es2015.promise", "es5"`
> 

##### Example {#example-2}

##### tsconfig.json {#tsconfigjson}

```
{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[lib ↗](https://www.typescriptlang.org/tsconfig.html#lib)": ["dom", "es2015.promise", "es5"]
  }
}
```

##### dramaticWelcome.ts {#dramaticwelcomets}

```ts
function delay(milliseconds: number) {
returnnewPromise<void>(resolve=> {
setTimeout(resolve, milliseconds);
  });
}

asyncfunctiondramaticWelcome() {
console.log("Hello");

for (leti = 0; i < 3; i++) {
awaitdelay(500);
console.log(".");
  }

console.log("World!");
}

dramaticWelcome();
```

Compiling and running the output should result in the correct behavior on an ES3/ES5 engine.

## Support for external helpers library (`tslib`) {#support-for-external-helpers-library-tslib}

TypeScript injects a handful of helper functions such as `__extends` for inheritance, `__assign` for spread operator in object literals and JSX elements, and `__awaiter` for async functions.

Previously there were two options:

1. inject helpers in *every* file that needs them, or
2. no helpers at all with [`noEmitHelpers` ↗](https://www.typescriptlang.org/tsconfig.html#noEmitHelpers).

The two options left more to be desired;
bundling the helpers in every file was a pain point for customers trying to keep their package size small.
And not including helpers, meant customers had to maintain their own helpers library.

TypeScript 2.1 allows for including these files in your project once in a separate module, and the compiler will emit imports to them as needed.

First, install the [`tslib` ↗](https://github.com/Microsoft/tslib) utility library:

```sh
npm install tslib
```

Second, compile your files using [`importHelpers` ↗](https://www.typescriptlang.org/tsconfig.html#importHelpers):

```sh
tsc --module commonjs --importHelpers a.ts
```

So given the following input, the resulting `.js` file will include an import to `tslib` and use the `__assign` helper from it instead of inlining it.

```ts
export const o = { a: 1, name: "o" };
exportconstcopy = { ...o };
```

```js
"use strict";
vartslib_1 = require("tslib");
exports.o = { a:1, name:"o" };
exports.copy = tslib_1.__assign({}, exports.o);
```

## Untyped imports {#untyped-imports}

TypeScript has traditionally been overly strict about how you can import modules.
This was to avoid typos and prevent users from using modules incorrectly.

However, a lot of the time, you might just want to import an existing module that may not have its own `.d.ts` file.
Previously this was an error.
Starting with TypeScript 2.1 this is now much easier.

With TypeScript 2.1, you can import a JavaScript module without needing a type declaration.
A type declaration (such as `declare module "foo" { ... }` or `node_modules/@types/foo`) still takes priority if it exists.

An import to a module with no declaration file will still be flagged as an error under [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny).

##### Example {#example-3}

```ts
// Succeeds if `node_modules/asdf/index.js` exists
import { x } from"asdf";
```

## Support for `--target ES2016`, `--target ES2017` and `--target ESNext` {#support-for---target-es2016---target-es2017-and---target-esnext}

TypeScript 2.1 supports three new target values `--target ES2016`, `--target ES2017` and `--target ESNext`.

Using target `--target ES2016` will instruct the compiler not to transform ES2016-specific features, e.g. `**` operator.

Similarly, `--target ES2017` will instruct the compiler not to transform ES2017-specific features like `async`/`await`.

`--target ESNext` targets latest supported [ES proposed features ↗](https://github.com/tc39/proposals).

## Improved `any` Inference {#improved-any-inference}

Previously, if TypeScript couldn’t figure out the type of a variable, it would choose the `any` type.

```ts
let x; // implicitly 'any'
lety = []; // implicitly 'any[]'

letz: any; // explicitly 'any'.
```

With TypeScript 2.1, instead of just choosing `any`, TypeScript will infer types based on what you end up assigning later on.

This is only enabled if [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny) is set.

##### Example {#example-4}

```ts
let x;

// You can still assign anything you want to 'x'.
x = () =>42;

// After that last assignment, TypeScript 2.1 knows that 'x' has type '() => number'.
lety = x();

// Thanks to that, it will now tell you that you can't add a number to a function!
console.log(x + y);
//          ~~~~~
// Error! Operator '+' cannot be applied to types '() => number' and 'number'.

// TypeScript still allows you to assign anything you want to 'x'.
x = "Hello world!";

// But now it also knows that 'x' is a 'string'!
x.toLowerCase();
```

The same sort of tracking is now also done for empty arrays.

A variable declared with no type annotation and an initial value of `[]` is considered an implicit `any[]` variable.
However, each subsequent `x.push(value)`, `x.unshift(value)` or `x[n] = value` operation *evolves* the type of the variable in accordance with what elements are added to it.

```ts
function f1() {
letx = [];
x.push(5);
x[1] = "hello";
x.unshift(true);
returnx; // (string | number | boolean)[]
}

functionf2() {
letx = null;
if (cond()) {
x = [];
while (cond()) {
x.push("hello");
    }
  }
returnx; // string[] | null
}
```

## Implicit any errors {#implicit-any-errors}

One great benefit of this is that you’ll see *way fewer* implicit `any` errors when running with [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny).
Implicit `any` errors are only reported when the compiler is unable to know the type of a variable without a type annotation.

##### Example {#example-5}

```ts
function f3() {
letx = []; // Error: Variable 'x' implicitly has type 'any[]' in some locations where its type cannot be determined.
x.push(5);
functiong() {
x; // Error: Variable 'x' implicitly has an 'any[]' type.
  }
}
```

## Better inference for literal types {#better-inference-for-literal-types}

String, numeric and boolean literal types (e.g. `"abc"`, `1`, and `true`) were previously inferred only in the presence of an explicit type annotation.
Starting with TypeScript 2.1, literal types are *always* inferred for `const` variables and `readonly` properties.

The type inferred for a `const` variable or `readonly` property without a type annotation is the type of the literal initializer.
The type inferred for a `let` variable, `var` variable, parameter, or non-`readonly` property with an initializer and no type annotation is the widened literal type of the initializer.
Where the widened type for a string literal type is `string`, `number` for numeric literal types, `boolean` for `true` or `false` and the containing enum for enum literal types.

##### Example {#example-6}

```ts
const c1 = 1; // Type 1
constc2 = c1; // Type 1
constc3 = "abc"; // Type "abc"
constc4 = true; // Type true
constc5 = cond ? 1 : "abc"; // Type 1 | "abc"

letv1 = 1; // Type number
letv2 = c2; // Type number
letv3 = c3; // Type string
letv4 = c4; // Type boolean
letv5 = c5; // Type number | string
```

Literal type widening can be controlled through explicit type annotations.
Specifically, when an expression of a literal type is inferred for a const location without a type annotation, that `const` variable gets a widening literal type inferred.
But when a `const` location has an explicit literal type annotation, the `const` variable gets a non-widening literal type.

##### Example {#example-7}

```ts
const c1 = "hello"; // Widening type "hello"
letv1 = c1; // Type string

constc2: "hello" = "hello"; // Type "hello"
letv2 = c2; // Type "hello"
```

## Use returned values from super calls as ‘this’ {#use-returned-values-from-super-calls-as-this}

In ES2015, constructors which return an object implicitly substitute the value of `this` for any callers of `super()`.
As a result, it is necessary to capture any potential return value of `super()` and replace it with `this`.
This change enables working with [Custom Elements ↗](https://www.w3.org/TR/custom-elements/), which takes advantage of this to initialize browser-allocated elements with user-written constructors.

##### Example {#example-8}

```ts
class Base {
x: number;
constructor() {
// return a new object other than `this`
return {
x:1
    };
  }
}

classDerivedextendsBase {
constructor() {
super();
this.x = 2;
  }
}
```

Generates:

```js
var Derived = (function(_super) {
__extends(Derived, _super);
functionDerived() {
var_this = _super.call(this) || this;
_this.x = 2;
return_this;
  }
returnDerived;
})(Base);
```

> This change entails a break in the behavior of extending built-in classes like `Error`, `Array`, `Map`, etc.. Please see the [extending built-ins breaking change documentation ↗](https://github.com/Microsoft/TypeScript-wiki/blob/master/Breaking-Changes.md#extending-built-ins-like-error-array-and-map-may-no-longer-work) for more details.
> 

## Configuration inheritance {#configuration-inheritance}

Often a project has multiple output targets, e.g. `ES5` and `ES2015`, debug and production or `CommonJS` and `System`;
Just a few configuration options change between these two targets, and maintaining multiple `tsconfig.json` files can be a hassle.

TypeScript 2.1 supports inheriting configuration using `extends`, where:

- `extends` is a new top-level property in `tsconfig.json` (alongside `compilerOptions`, [`files` ↗](https://www.typescriptlang.org/tsconfig.html#files), [`include` ↗](https://www.typescriptlang.org/tsconfig.html#include), and [`exclude` ↗](https://www.typescriptlang.org/tsconfig.html#exclude)).
- The value of `extends` must be a string containing a path to another configuration file to inherit from.
- The configuration from the base file are loaded first, then overridden by those in the inheriting config file.
- Circularity between configuration files is not allowed.
- [`files` ↗](https://www.typescriptlang.org/tsconfig.html#files), [`include` ↗](https://www.typescriptlang.org/tsconfig.html#include), and [`exclude` ↗](https://www.typescriptlang.org/tsconfig.html#exclude) from the inheriting config file *overwrite* those from the base config file.
- All relative paths found in the configuration file will be resolved relative to the configuration file they originated in.

##### Example {#example-9}

`configs/base.json`:

```
{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)": true,
"[strictNullChecks ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks)": true
  }
}
```

`tsconfig.json`:

```
{
"[extends ↗](https://www.typescriptlang.org/tsconfig.html#extends)": "./configs/base",
"[files ↗](https://www.typescriptlang.org/tsconfig.html#files)": ["main.ts", "supplemental.ts"]
}
```

`tsconfig.nostrictnull.json`:

```
{
"[extends ↗](https://www.typescriptlang.org/tsconfig.html#extends)": "./tsconfig",
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[strictNullChecks ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks)": false
  }
}
```

## New `--alwaysStrict` {#new---alwaysstrict}

Invoking the compiler with [`alwaysStrict` ↗](https://www.typescriptlang.org/tsconfig.html#alwaysStrict) causes:

1. Parses all the code in strict mode.
2. Writes `"use strict";` directive atop every generated file.

Modules are parsed automatically in strict mode.
The new flag is recommended for non-module code.
