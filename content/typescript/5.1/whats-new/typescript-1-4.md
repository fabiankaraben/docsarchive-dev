---
linkTitle: "TypeScript 1.4"
title: "TypeScript: Documentation - TypeScript 1.4"
description: "TypeScript 1.3 Release Notes"
weight: 37
type: docs
---

# TypeScript 1.4

## Union types {#union-types}

### Overview {#overview}

Union types are a powerful way to express a value that can be one of several types. For example, you might have an API for running a program that takes a commandline as either a `string`, a `string[]` or a function that returns a `string`. You can now write:

```ts
interface RunOptions {
program: string;
commandline: string[] | string | (() =>string);
}
```

Assignment to union types works very intuitively — anything you could assign to one of the union type’s members is assignable to the union:

```ts
var opts: RunOptions = /* ... */;
opts.commandline = '-hello world'; // OK
opts.commandline = ['-hello', 'world']; // OK
opts.commandline = [42]; // Error, number is not string or string[]
```

When reading from a union type, you can see any properties that are shared by them:

```ts
if (opts.commandline.length === 0) {
// OK, string and string[] both have 'length' property
console.log("it's empty");
}
```

Using Type Guards, you can easily work with a variable of a union type:

```ts
function formatCommandline(c: string | string[]) {
if (typeofc === "string") {
returnc.trim();
  } else {
returnc.join(" ");
  }
}
```

### Stricter Generics {#stricter-generics}

With union types able to represent a wide range of type scenarios, we’ve decided to improve the strictness of certain generic calls. Previously, code like this would (surprisingly) compile without error:

```ts
function equal<T>(lhs: T, rhs: T): boolean {
returnlhs === rhs;
}

// Previously: No error
// New behavior: Error, no best common type between 'string' and 'number'
vare = equal(42, "hello");
```

With union types, you can now specify the desired behavior at both the function declaration site and the call site:

```ts
// 'choose' function where types must match
functionchoose1<T>(a: T, b: T): T {
returnMath.random() > 0.5 ? a : b;
}
vara = choose1("hello", 42); // Error
varb = choose1<string | number>("hello", 42); // OK

// 'choose' function where types need not match
functionchoose2<T, U>(a: T, b: U): T | U {
returnMath.random() > 0.5 ? a : b;
}
varc = choose2("bar", "foo"); // OK, c: string
vard = choose2("hello", 42); // OK, d: string|number
```

### Better Type Inference {#better-type-inference}

Union types also allow for better type inference in arrays and other places where you might have multiple kinds of values in a collection:

```ts
var x = [1, "hello"]; // x: Array<string|number>
x[0] = "world"; // OK
x[0] = false; // Error, boolean is not string or number
```

## `let` declarations {#let-declarations}

In JavaScript, `var` declarations are “hoisted” to the top of their enclosing scope. This can result in confusing bugs:

```ts
console.log(x); // meant to write 'y' here
/* later in the same block */
varx = "hello";
```

The new ES6 keyword `let`, now supported in TypeScript, declares a variable with more intuitive “block” semantics. A `let` variable can only be referred to after its declaration, and is scoped to the syntactic block where it is defined:

```ts
if (foo) {
console.log(x); // Error, cannot refer to x before its declaration
letx = "hello";
} else {
console.log(x); // Error, x is not declared in this block
}
```

`let` is only available when targeting ECMAScript 6 (`--target ES6`).

## `const` declarations {#const-declarations}

The other new ES6 declaration type supported in TypeScript is `const`. A `const` variable may not be assigned to, and must be initialized where it is declared. This is useful for declarations where you don’t want to change the value after its initialization:

```ts
const halfPi = Math.PI / 2;
halfPi = 2; // Error, can't assign to a `const`
```

`const` is only available when targeting ECMAScript 6 (`--target ES6`).

## Template strings {#template-strings}

TypeScript now supports ES6 template strings. These are an easy way to embed arbitrary expressions in strings:

```ts
var name = "TypeScript";
vargreeting = `Hello, ${name}! Your name has ${name.length} characters`;
```

When compiling to pre-ES6 targets, the string is decomposed:

```js
var name = "TypeScript!";
vargreeting =
"Hello, " + name + "! Your name has " + name.length + " characters";
```

## Type Guards {#type-guards}

A common pattern in JavaScript is to use `typeof` or `instanceof` to examine the type of an expression at runtime. TypeScript now understands these conditions and will change type inference accordingly when used in an `if` block.

Using `typeof` to test a variable:

```ts
var x: any = /* ... */;
if(typeofx === 'string') {
console.log(x.subtr(1)); // Error, 'subtr' does not exist on 'string'
}
// x is still any here
x.unknown(); // OK
```

Using `typeof` with union types and `else`:

```ts
var x: string | HTMLElement = /* ... */;
if(typeofx === 'string') {
// x is string here, as shown above
}
else {
// x is HTMLElement here
console.log(x.innerHTML);
}
```

Using `instanceof` with classes and union types:

```ts
class Dog { woof() { } }
classCat { meow() { } }
varpet: Dog|Cat = /* ... */;
if (petinstanceofDog) {
pet.woof(); // OK
}
else {
pet.woof(); // Error
}
```

## Type Aliases {#type-aliases}

You can now define an *alias* for a type using the `type` keyword:

```ts
type PrimitiveArray = Array<string | number | boolean>;
typeMyNumber = number;
typeNgScope = ng.IScope;
typeCallback = () =>void;
```

Type aliases are exactly the same as their original types; they are simply alternative names.

## `const enum` (completely inlined enums) {#const-enum-completely-inlined-enums}

Enums are very useful, but some programs don’t actually need the generated code and would benefit from simply inlining all instances of enum members with their numeric equivalents. The new `const enum` declaration works just like a regular `enum` for type safety, but erases completely at compile time.

```ts
const enum Suit {
Clubs,
Diamonds,
Hearts,
Spades
}
vard = Suit.Diamonds;
```

Compiles to exactly:

```js
var d = 1;
```

TypeScript will also now compute enum values when possible:

```ts
enum MyFlags {
None = 0,
Neat = 1,
Cool = 2,
Awesome = 4,
Best = Neat | Cool | Awesome
}
varb = MyFlags.Best; // emits var b = 7;
```

## `-noEmitOnError` commandline option {#-noemitonerror-commandline-option}

The default behavior for the TypeScript compiler is to still emit .js files if there were type errors (for example, an attempt to assign a `string` to a `number`). This can be undesirable on build servers or other scenarios where only output from a “clean” build is desired. The new flag [`noEmitOnError` ↗](https://www.typescriptlang.org/tsconfig.html#noEmitOnError) prevents the compiler from emitting .js code if there were any errors.

This is now the default for MSBuild projects; this allows MSBuild incremental build to work as expected, as outputs are only generated on clean builds.

## AMD Module names {#amd-module-names}

By default AMD modules are generated anonymous. This can lead to problems when other tools are used to process the resulting modules like a bundlers (e.g. `r.js`).

The new `amd-module name` tag allows passing an optional module name to the compiler:

```ts
//// [amdModule.ts]
///<amd-modulename='NamedModule'/>
exportclassC {}
```

Will result in assigning the name `NamedModule` to the module as part of calling the AMD `define`:

```js
//// [amdModule.js]
define("NamedModule", ["require", "exports"], function(require, exports) {
varC = (function() {
functionC() {}
returnC;
  })();
exports.C = C;
});
```
