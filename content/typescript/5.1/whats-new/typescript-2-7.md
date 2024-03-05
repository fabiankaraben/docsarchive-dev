---
linkTitle: "TypeScript 2.7"
title: "TypeScript: Documentation - TypeScript 2.7"
description: "TypeScript 2.7 Release Notes"
weight: 25
type: docs
canonical: /typescript/5.2/whats-new/typescript-2-7
---

# TypeScript 2.7

## Constant-named properties {#constant-named-properties}

TypeScript 2.7 adds support for declaring const-named properties on types including ECMAScript symbols.

##### Example {#example}

```ts
// Lib
exportconstSERIALIZE = Symbol("serialize-method-key");

exportinterfaceSerializable {
  [SERIALIZE ↗](https://www.typescriptlang.orgobj: {}): string;
}
```

```ts
// consumer

import { SERIALIZE, Serializable } from"lib";

classJSONSerializableItemimplementsSerializable {
  [SERIALIZE ↗](https://www.typescriptlang.orgobj: {}) {
returnJSON.stringify(obj);
  }
}
```

This also applies to numeric and string literals.

##### Example {#example-1}

```ts
const Foo = "Foo";
constBar = "Bar";

letx = {
[Foo]:100,
[Bar]:"hello"
};

leta = x[Foo]; // has type 'number'
letb = x[Bar]; // has type 'string'
```

## `unique symbol` {#unique-symbol}

To enable treating symbols as unique literals a new type `unique symbol` is available.
`unique symbol` is a subtype of `symbol`, and are produced only from calling `Symbol()` or `Symbol.for()`, or from explicit type annotations.
The new type is only allowed on `const` declarations and `readonly static` properties, and in order to reference a specific unique symbol, you’ll have to use the `typeof` operator.
Each reference to a `unique symbol` implies a completely unique identity that’s tied to a given declaration.

##### Example {#example-2}

```ts
// Works
declareconstFoo: uniquesymbol;

// Error! 'Bar' isn't a constant.
letBar: uniquesymbol = Symbol();

// Works - refers to a unique symbol, but its identity is tied to 'Foo'.
letBaz: typeofFoo = Foo;

// Also works.
classC {
staticreadonlyStaticSymbol: uniquesymbol = Symbol();
}
```

Because each `unique symbol` has a completely separate identity, no two `unique symbol` types are assignable or comparable to each other.

##### Example {#example-3}

```ts
const Foo = Symbol();
constBar = Symbol();

// Error: can't compare two unique symbols.
if (Foo === Bar) {
// ...
}
```

## Strict Class Initialization {#strict-class-initialization}

TypeScript 2.7 introduces a new flag called [`strictPropertyInitialization` ↗](https://www.typescriptlang.org/tsconfig.html#strictPropertyInitialization).
This flag performs checks to ensure that each instance property of a class gets initialized in the constructor body, or by a property initializer.
For example

```ts
class C {
foo: number;
bar = "hello";
baz: boolean;
//  ~~~
//  Error! Property 'baz' has no initializer and is not definitely assigned in the
//         constructor.

constructor() {
this.foo = 42;
  }
}
```

In the above, if we truly meant for `baz` to potentially be `undefined`, we should have declared it with the type `boolean | undefined`.

There are certain scenarios where properties can be initialized indirectly (perhaps by a helper method or dependency injection library), in which case you can use the new *definite assignment assertion modifiers* for your properties (discussed below).

```ts
class C {
foo!: number;
// ^
// Notice this '!' modifier.
// This is the "definite assignment assertion"

constructor() {
this.initialize();
  }

initialize() {
this.foo = 0;
  }
}
```

Keep in mind that [`strictPropertyInitialization` ↗](https://www.typescriptlang.org/tsconfig.html#strictPropertyInitialization) will be turned on along with other [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict) mode flags, which can impact your project.
You can set the [`strictPropertyInitialization` ↗](https://www.typescriptlang.org/tsconfig.html#strictPropertyInitialization) setting to `false` in your `tsconfig.json`’s `compilerOptions`, or `--strictPropertyInitialization false` on the command line to turn off this checking.

## Definite Assignment Assertions {#definite-assignment-assertions}

The definite assignment assertion is a feature that allows a `!` to be placed after instance property and variable declarations to relay to TypeScript that a variable is indeed assigned for all intents and purposes, even if TypeScript’s analyses cannot detect so.

##### Example {#example-4}

```ts
let x: number;
initialize();
console.log(x + x);
//          ~   ~
// Error! Variable 'x' is used before being assigned.

functioninitialize() {
x = 10;
}
```

With definite assignment assertions, we can assert that `x` is really assigned by appending an `!` to its declaration:

```ts
// Notice the '!'
letx!: number;
initialize();

// No error!
console.log(x + x);

functioninitialize() {
x = 10;
}
```

In a sense, the definite assignment assertion operator is the dual of the non-null assertion operator (in which *expressions* are post-fixed with a `!`), which we could also have used in the example.

```ts
let x: number;
initialize();

// No error!
console.log(x! + x!);

functioninitialize() {
x = 10;
}
```

In our example, we knew that all uses of `x` would be initialized so it makes more sense to use definite assignment assertions than non-null assertions.

## Fixed Length Tuples {#fixed-length-tuples}

In TypeScript 2.6 and earlier, `[number, string, string]` was considered a subtype of `[number, string]`.
This was motivated by TypeScript’s structural nature; the first and second elements of a `[number, string, string]` are respectively subtypes of the first and second elements of `[number, string]`.
However, after examining real world usage of tuples, we noticed that most situations in which this was permitted was typically undesirable.

In TypeScript 2.7, tuples of different arities are no longer assignable to each other.
Thanks to a pull request from [Kiara Grouwstra ↗](https://github.com/KiaraGrouwstra), tuple types now encode their arity into the type of their respective `length` property.
This is accomplished by leveraging numeric literal types, which now allow tuples to be distinct from tuples of different arities.

Conceptually, you might consider the type `[number, string]` to be equivalent to the following declaration of `NumStrTuple`:

```ts
interface NumStrTuple extends Array<number | string> {
0: number;
1: string;
length: 2; // using the numeric literal type '2'
}
```

Note that this is a breaking change for some code.
If you need to resort to the original behavior in which tuples only enforce a minimum length, you can use a similar declaration that does not explicitly define a `length` property, falling back to `number`.

```ts
interface MinimumNumStrTuple extends Array<number | string> {
0: number;
1: string;
}
```

Note that this does not imply tuples represent immutable arrays, but it is an implied convention.

## Improved type inference for object literals {#improved-type-inference-for-object-literals}

TypeScript 2.7 improves type inference for multiple object literals occurring in the same context.
When multiple object literal types contribute to a union type, we now *normalize* the object literal types such that all properties are present in each constituent of the union type.

Consider:

```ts
const obj = test ? { text: "hello" } : {}; // { text: string } | { text?: undefined }
consts = obj.text; // string | undefined
```

Previously type `{}` was inferred for `obj` and the second line subsequently caused an error because `obj` would appear to have no properties.
That obviously wasn’t ideal.

##### Example {#example-5}

```ts
// let obj: { a: number, b: number } |
//     { a: string, b?: undefined } |
//     { a?: undefined, b?: undefined }
letobj = [{ a:1, b:2 }, { a:"abc" }, {}][0];
obj.a; // string | number | undefined
obj.b; // number | undefined
```

Multiple object literal type inferences for the same type parameter are similarly collapsed into a single normalized union type:

```ts
declare function f<T>(...items: T[]): T;
// let obj: { a: number, b: number } |
//     { a: string, b?: undefined } |
//     { a?: undefined, b?: undefined }
letobj = f({ a:1, b:2 }, { a:"abc" }, {});
obj.a; // string | number | undefined
obj.b; // number | undefined
```

## Improved handling of structurally identical classes and `instanceof` expressions {#improved-handling-of-structurally-identical-classes-and-instanceof-expressions}

TypeScript 2.7 improves the handling of structurally identical classes in union types and `instanceof` expressions:

- Structurally identical, but distinct, class types are now preserved in union types (instead of eliminating all but one).
- Union type subtype reduction only removes a class type if it is a subclass of *and* derives from another class type in the union.
- Type checking of the `instanceof` operator is now based on whether the type of the left operand *derives from* the type indicated by the right operand (as opposed to a structural subtype check).

This means that union types and `instanceof` properly distinguish between structurally identical classes.

##### Example {#example-6}

```ts
class A {}
classBextendsA {}
classCextendsA {}
classDextendsA {
c: string;
}
classEextendsD {}

letx1 = !true ? newA() : newB(); // A
letx2 = !true ? newB() : newC(); // B | C (previously B)
letx3 = !true ? newC() : newD(); // C | D (previously C)

leta1 = [newA(), newB(), newC(), newD(), newE()]; // A[]
leta2 = [newB(), newC(), newD(), newE()]; // (B | C | D)[] (previously B[])

functionf1(x: B | C | D) {
if (xinstanceofB) {
x; // B (previously B | D)
  } elseif (xinstanceofC) {
x; // C
  } else {
x; // D (previously never)
  }
}
```

## Type guards inferred from `in` operator {#type-guards-inferred-from-in-operator}

The `in` operator now acts as a narrowing expression for types.

For a `n in x` expression, where `n` is a string literal or string literal type and `x` is a union type, the “true” branch narrows to types which have an optional or required property `n`, and the “false” branch narrows to types which have an optional or missing property `n`.

##### Example {#example-7}

```ts
interface A {
a: number;
}
interfaceB {
b: string;
}

functionfoo(x: A | B) {
if ("a"inx) {
returnx.a;
  }
returnx.b;
}
```

## Support for `import d from "cjs"` from CommonJS modules with `--esModuleInterop` {#support-for-import-d-from-cjs-from-commonjs-modules-with---esmoduleinterop}

TypeScript 2.7 updates CommonJS/AMD/UMD module emit to synthesize namespace records based on the presence of an `__esModule` indicator under [`esModuleInterop` ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop).
The change brings the generated output from TypeScript closer to that generated by Babel.

Previously CommonJS/AMD/UMD modules were treated in the same way as ES6 modules, resulting in a couple of problems. Namely:

- TypeScript treats a namespace import (i.e. `import * as foo from "foo"`) for a CommonJS/AMD/UMD module as equivalent to `const foo = require("foo")`.Things are simple here, but they don’t work out if the primary object being imported is a primitive or a class or a function. ECMAScript spec stipulates that a namespace record is a plain object, and that a namespace import (`foo` in the example above) is not callable, though allowed by TypeScript
- Similarly a default import (i.e. `import d from "foo"`) for a CommonJS/AMD/UMD module as equivalent to `const d = require("foo").default`.Most of the CommonJS/AMD/UMD modules available today do not have a `default` export, making this import pattern practically unusable to import non-ES modules (i.e. CommonJS/AMD/UMD). For instance `import fs from "fs"` or `import express from "express"` are not allowed.

Under the new [`esModuleInterop` ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop) these two issues should be addressed:

- A namespace import (i.e. `import * as foo from "foo"`) is now correctly flagged as uncallable. Calling it will result in an error.
- Default imports to CommonJS/AMD/UMD are now allowed (e.g. `import fs from "fs"`), and should work as expected.

> Note: The new behavior is added under a flag to avoid unwarranted breaks to existing code bases. **We highly recommend applying it both to new and existing projects.**
> For existing projects, namespace imports (`import * as express from "express"; express();`) will need to be converted to default imports (`import express from "express"; express();`).
> 

##### Example {#example-8}

With [`esModuleInterop` ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop) two new helpers are generated `__importStar` and `__importDefault` for import `*` and import `default` respectively.
For instance input like:

```ts
import * as foo from "foo";
importbfrom"bar";
```

Will generate:

```js
"use strict";
var__importStar =
  (this && this.__importStar) ||
function(mod) {
if (mod && mod.__esModule) returnmod;
varresult = {};
if (mod != null)
for (varkinmod)
if (Object.hasOwnProperty.call(mod, k)) result[k] = mod[k];
result["default"] = mod;
returnresult;
  };
var__importDefault =
  (this && this.__importDefault) ||
function(mod) {
returnmod && mod.__esModule ? mod : { default:mod };
  };
exports.__esModule = true;
varfoo = __importStar(require("foo"));
varbar_1 = __importDefault(require("bar"));
```

## Numeric separators {#numeric-separators}

TypeScript 2.7 brings support for [ES Numeric Separators ↗](https://github.com/tc39/proposal-numeric-separator).
Numeric literals can now be separated into segments using `_`.

##### Example {#example-9}

```ts
const million = 1_000_000;
constphone = 555_734_2231;
constbytes = 0xff_0c_00_ff;
constword = 0b1100_0011_1101_0001;
```

## Cleaner output in `--watch` mode {#cleaner-output-in---watch-mode}

TypeScript’s `--watch` mode now clears the screen after a re-compilation is requested.

## Prettier `--pretty` output {#prettier---pretty-output}

TypeScript’s [`pretty` ↗](https://www.typescriptlang.org/tsconfig.html#pretty) flag can make error messages easier to read and manage.
[`pretty` ↗](https://www.typescriptlang.org/tsconfig.html#pretty) now uses colors for file names, diagnostic codes, and line numbers.
File names and positions are now also formatted to allow navigation in common terminals (e.g. Visual Studio Code terminal).
