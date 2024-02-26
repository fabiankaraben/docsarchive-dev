---
linkTitle: "TypeScript 1.5"
title: "TypeScript: Documentation - TypeScript 1.5"
description: "TypeScript 1.5 Release Notes"
weight: 36
type: docs
---

# TypeScript 1.5

## ES6 Modules {#es6-modules}

TypeScript 1.5 supports ECMAScript 6 (ES6) modules.
ES6 modules are effectively TypeScript external modules with a new syntax: ES6 modules are separately loaded source files that possibly import other modules and provide a number of externally accessible exports.
ES6 modules feature several new export and import declarations.
It is recommended that TypeScript libraries and applications be updated to use the new syntax, but this is not a requirement.
The new ES6 module syntax coexists with TypeScript’s original internal and external module constructs and the constructs can be mixed and matched at will.

#### Export Declarations {#export-declarations}

In addition to the existing TypeScript support for decorating declarations with `export`, module members can also be exported using separate export declarations, optionally specifying different names for exports using `as` clauses.

```ts
interface Stream { ... }
functionwriteToStream(stream: Stream, data: string) { ... }
export { Stream, writeToStreamaswrite };  // writeToStream exported as write
```

Import declarations, as well, can optionally use `as` clauses to specify different local names for the imports. For example:

```ts
import { read, write, standardOutput as stdout } from "./inout";
vars = read(stdout);
write(stdout, s);
```

As an alternative to individual imports, a namespace import can be used to import an entire module:

```ts
import * as io from "./inout";
vars = io.read(io.standardOutput);
io.write(io.standardOutput, s);
```

#### Re-exporting {#re-exporting}

Using `from` clause a module can copy the exports of a given module to the current module without introducing local names.

```ts
export { read, write, standardOutput as stdout } from "./inout";
```

`export *` can be used to re-export all exports of another module. This is useful for creating modules that aggregate the exports of several other modules.

```ts
export function transform(s: string): string { ... }
export*from"./mod1";
export*from"./mod2";
```

#### Default Export {#default-export}

An export default declaration specifies an expression that becomes the default export of a module:

```ts
export default class Greeter {
sayHello() {
console.log("Greetings!");
  }
}
```

Which in turn can be imported using default imports:

```ts
import Greeter from "./greeter";
varg = newGreeter();
g.sayHello();
```

#### Bare Import {#bare-import}

A “bare import” can be used to import a module only for its side-effects.

```ts
import "./polyfills";
```

For more information about module, please see the [ES6 module support spec ↗](https://github.com/Microsoft/TypeScript/issues/2242).

## Destructuring in declarations and assignments {#destructuring-in-declarations-and-assignments}

TypeScript 1.5 adds support to ES6 destructuring declarations and assignments.

#### Declarations {#declarations}

A destructuring declaration introduces one or more named variables and initializes them with values extracted from properties of an object or elements of an array.

For example, the following sample declares variables `x`, `y`, and `z`, and initializes them to `getSomeObject().x`, `getSomeObject().y` and `getSomeObject().z` respectively:

```ts
var { x, y, z } = getSomeObject();
```

Destructuring declarations also works for extracting values from arrays:

```ts
var [x, y, z = 10] = getSomeArray();
```

Similarly, destructuring can be used in function parameter declarations:

```ts
function drawText({ text = "", location: [x, y] = [0, 0], bold = false }) {
// Draw text
}

// Call drawText with an object literal
varitem = { text:"someText", location: [1, 2, 3], style:"italics" };
drawText(item);
```

#### Assignments {#assignments}

Destructuring patterns can also be used in regular assignment expressions.
For instance, swapping two variables can be written as a single destructuring assignment:

```ts
var x = 1;
vary = 2;
[x, y] = [y, x];
```

## `namespace` keyword {#namespace-keyword}

TypeScript used the `module` keyword to define both “internal modules” and “external modules”;
this has been a bit of confusion for developers new to TypeScript.
“Internal modules” are closer to what most people would call a namespace; likewise, “external modules” in JS speak really just are modules now.

> Note: Previous syntax defining internal modules are still supported.
> 

**Before**:

```ts
module Math {
exportfunctionadd(x, y) { ... }
}
```

**After**:

```ts
namespace Math {
exportfunctionadd(x, y) { ... }
}
```

## `let` and `const` support {#let-and-const-support}

ES6 `let` and `const` declarations are now supported when targeting ES3 and ES5.

#### Const {#const}

```ts
const MAX = 100;

++MAX; // Error: The operand of an increment or decrement
//        operator cannot be a constant.
```

#### Block scoped {#block-scoped}

```ts
if (true) {
leta = 4;
// use a
} else {
leta = "string";
// use a
}

alert(a); // Error: a is not defined in this scope.
```

## for..of support {#forof-support}

TypeScript 1.5 adds support to ES6 for..of loops on arrays for ES3/ES5 as well as full support for Iterator interfaces when targeting ES6.

##### Example {#example}

The TypeScript compiler will transpile for..of arrays to idiomatic ES3/ES5 JavaScript when targeting those versions:

```ts
for (var v of expr) {
}
```

will be emitted as:

```js
for (var _i = 0, _a = expr; _i < _a.length; _i++) {
varv = _a[_i];
}
```

## Decorators {#decorators}

> TypeScript decorators are based on the [ES7 decorator proposal ↗](https://github.com/wycats/javascript-decorators).
> 

A decorator is:

- an expression
- that evaluates to a function
- that takes the target, name, and property descriptor as arguments
- and optionally returns a property descriptor to install on the target object

> For more information, please see the [Decorators ↗](https://github.com/Microsoft/TypeScript/issues/2249) proposal.
> 

##### Example {#example-1}

Decorators `readonly` and `enumerable(false)` will be applied to the property `method` before it is installed on class `C`.
This allows the decorator to change the implementation, and in this case, augment the descriptor to be writable: false and enumerable: false.

```ts
class C {
  @readonly
  @enumerable(false)
method() { ... }
}

functionreadonly(target, key, descriptor) {
descriptor.writable = false;
}

functionenumerable(value) {
returnfunction (target, key, descriptor) {
descriptor.enumerable = value;
    };
}
```

## Computed properties {#computed-properties}

Initializing an object with dynamic properties can be a bit of a burden. Take the following example:

```ts
type NeighborMap = { [name: string]: Node };
typeNode = { name: string; neighbors: NeighborMap };

functionmakeNode(name: string, initialNeighbor: Node): Node {
varneighbors: NeighborMap = {};
neighbors[initialNeighbor.name] = initialNeighbor;
return { name:name, neighbors:neighbors };
}
```

Here we need to create a variable to hold on to the neighbor-map so that we can initialize it.
With TypeScript 1.5, we can let the compiler do the heavy lifting:

```ts
function makeNode(name: string, initialNeighbor: Node): Node {
return {
name:name,
neighbors: {
[initialNeighbor.name]:initialNeighbor,
    },
  };
}
```

## Support for `UMD` and `System` module output {#support-for-umd-and-system-module-output}

In addition to `AMD` and `CommonJS` module loaders, TypeScript now supports emitting modules `UMD` ([Universal Module Definition ↗](https://github.com/umdjs/umd)) and [`System` ↗](https://github.com/systemjs/systemjs) module formats.

**Usage**:

> tsc —module umd
> 

and

> tsc —module system
> 

## Unicode codepoint escapes in strings {#unicode-codepoint-escapes-in-strings}

ES6 introduces escapes that allow users to represent a Unicode codepoint using just a single escape.

As an example, consider the need to escape a string that contains the character ’𠮷‘.
In UTF-16/UCS2, ’𠮷’ is represented as a surrogate pair, meaning that it’s encoded using a pair of 16-bit code units of values, specifically `0xD842` and `0xDFB7`.
Previously this meant that you’d have to escape the codepoint as `"\uD842\uDFB7"`.
This has the major downside that it’s difficult to discern two independent characters from a surrogate pair.

With ES6’s codepoint escapes, you can cleanly represent that exact character in strings and template strings with a single escape: `"\u{20bb7}"`.
TypeScript will emit the string in ES3/ES5 as `"\uD842\uDFB7"`.

## Tagged template strings in ES3/ES5 {#tagged-template-strings-in-es3es5}

In TypeScript 1.4, we added support for template strings for all targets, and tagged templates for just ES6.
Thanks to some considerable work done by [@ivogabe ↗](https://github.com/ivogabe), we bridged the gap for for tagged templates in ES3 and ES5.

When targeting ES3/ES5, the following code

```ts
function oddRawStrings(strs: TemplateStringsArray, n1, n2) {
returnstrs.raw.filter((raw, index) =>index % 2 === 1);
}

oddRawStrings`Hello \n${123}\t${456}\n world`;
```

will be emitted as

```js
function oddRawStrings(strs, n1, n2) {
returnstrs.raw.filter(function (raw, index) {
returnindex % 2 === 1;
  });
}
(_a = ["Hello \n", " \t ", "\n world"]),
  (_a.raw = ["Hello \\n", " \\t ", "\\n world"]),
oddRawStrings(_a, 123, 456);
var_a;
```

## AMD-dependency optional names {#amd-dependency-optional-names}

`/// <amd-dependency path="x" />` informs the compiler about a non-TS module dependency that needs to be injected in the resulting module’s require call;
however, there was no way to consume this module in the TS code.

The new `amd-dependency name` property allows passing an optional name for an amd-dependency:

```ts
/// <amd-dependency path="legacy/moduleA" name="moduleA"/>
declarevarmoduleA: MyType;
moduleA.callStuff();
```

Generated JS code:

```js
define(["require", "exports", "legacy/moduleA"], function (
require,
exports,
moduleA
) {
moduleA.callStuff();
});
```

## Project support through `tsconfig.json` {#project-support-through-tsconfigjson}

Adding a `tsconfig.json` file in a directory indicates that the directory is the root of a TypeScript project.
The tsconfig.json file specifies the root files and the compiler options required to compile the project. A project is compiled in one of the following ways:

- By invoking tsc with no input files, in which case the compiler searches for the tsconfig.json file starting in the current directory and continuing up the parent directory chain.
- By invoking tsc with no input files and a -project (or just -p) command line option that specifies the path of a directory containing a tsconfig.json file.

##### Example {#example-2}

```
{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[module ↗](https://www.typescriptlang.org/tsconfig.html#module)": "commonjs",
"[noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)": true,
"[sourceMap ↗](https://www.typescriptlang.org/tsconfig.html#sourceMap)": true
  }
}
```

See the [tsconfig.json wiki page ↗](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json) for more details.

## `--rootDir` command line option {#--rootdir-command-line-option}

Option [`outDir` ↗](https://www.typescriptlang.org/tsconfig.html#outDir) duplicates the input hierarchy in the output.
The compiler computes the root of the input files as the longest common path of all input files;
and then uses that to replicate all its substructure in the output.

Sometimes this is not desirable, for instance inputs `FolderA\FolderB\1.ts` and `FolderA\FolderB\2.ts` would result in output structure mirroring `FolderA\FolderB\`.
Now if a new file `FolderA\3.ts` is added to the input, the output structure will pop out to mirror `FolderA\`.

[`rootDir` ↗](https://www.typescriptlang.org/tsconfig.html#rootDir) specifies the input directory to be mirrored in output instead of computing it.

## `--noEmitHelpers` command line option {#--noemithelpers-command-line-option}

The TypeScript compiler emits a few helpers like `__extends` when needed.
The helpers are emitted in every file they are referenced in.
If you want to consolidate all helpers in one place, or override the default behavior, use [`noEmitHelpers` ↗](https://www.typescriptlang.org/tsconfig.html#noEmitHelpers) to instructs the compiler not to emit them.

## `--newLine` command line option {#--newline-command-line-option}

By default the output new line character is `\r\n` on Windows based systems and `\n` on *nix based systems.
[`newLine` ↗](https://www.typescriptlang.org/tsconfig.html#newLine) command line flag allows overriding this behavior and specifying the new line character to be used in generated output files.

## `--inlineSourceMap` and `inlineSources` command line options {#--inlinesourcemap-and-inlinesources-command-line-options}

[`inlineSourceMap` ↗](https://www.typescriptlang.org/tsconfig.html#inlineSourceMap) causes source map files to be written inline in the generated `.js` files instead of in a independent `.js.map` file.
[`inlineSources` ↗](https://www.typescriptlang.org/tsconfig.html#inlineSources) allows for additionally inlining the source `.ts` file into the `.js` file.
