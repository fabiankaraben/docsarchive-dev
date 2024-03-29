---
linkTitle: "TypeScript 1.8"
title: "TypeScript: Documentation - TypeScript 1.8"
description: "TypeScript 1.8 Release Notes"
weight: 33
type: docs
canonical: /typescript/5.2/whats-new/typescript-1-8
---

# TypeScript 1.8

## Type parameters as constraints {#type-parameters-as-constraints}

With TypeScript 1.8 it becomes possible for a type parameter constraint to reference type parameters from the same type parameter list.
Previously this was an error.
This capability is usually referred to as [F-Bounded Polymorphism ↗](https://wikipedia.org/wiki/Bounded_quantification#F-bounded_quantification).

##### Example {#example}

```ts
function assign<T extends U, U>(target: T, source: U): T {
for (letidinsource) {
target[id] = source[id];
  }
returntarget;
}

letx = { a:1, b:2, c:3, d:4 };
assign(x, { b:10, d:20 });
assign(x, { e:0 }); // Error
```

## Control flow analysis errors {#control-flow-analysis-errors}

TypeScript 1.8 introduces control flow analysis to help catch common errors that users tend to run into.
Read on to get more details, and check out these errors in action:

![cfa](/assets/typescript/5.1/external/cloud.githubusercontent.com/assets/8052307/5210657/c5ae0f28-7585-11e4-97d8-86169ef2a160.gif)

### Unreachable code {#unreachable-code}

Statements guaranteed to not be executed at run time are now correctly flagged as unreachable code errors.
For instance, statements following unconditional `return`, `throw`, `break` or `continue` statements are considered unreachable.
Use [`allowUnreachableCode`](/tsconfig.html#allowUnreachableCode) to disable unreachable code detection and reporting.

##### Example {#example-1}

Here’s a simple example of an unreachable code error:

```ts
function f(x) {
if (x) {
returntrue;
  } else {
returnfalse;
  }

x = 0; // Error: Unreachable code detected.
}
```

A more common error that this feature catches is adding a newline after a `return` statement:

```ts
function f() {
return; // Automatic Semicolon Insertion triggered at newline
  {
    x: "string"; // Error: Unreachable code detected.
  }
}
```

Since JavaScript automatically terminates the `return` statement at the end of the line, the object literal becomes a block.

### Unused labels {#unused-labels}

Unused labels are also flagged.
Just like unreachable code checks, these are turned on by default;
use [`allowUnusedLabels`](/tsconfig.html#allowUnusedLabels) to stop reporting these errors.

##### Example {#example-2}

```ts
loop: while (x > 0) {
// Error: Unused label.
x++;
}
```

### Implicit returns {#implicit-returns}

Functions with code paths that do not return a value in JS implicitly return `undefined`.
These can now be flagged by the compiler as implicit returns.
The check is turned *off* by default; use [`noImplicitReturns`](/tsconfig.html#noImplicitReturns) to turn it on.

##### Example {#example-3}

```ts
function f(x) {
// Error: Not all code paths return a value.
if (x) {
returnfalse;
  }

// implicitly returns `undefined`
}
```

### Case clause fall-throughs {#case-clause-fall-throughs}

TypeScript can reports errors for fall-through cases in switch statement where the case clause is non-empty.
This check is turned *off* by default, and can be enabled using [`noFallthroughCasesInSwitch`](/tsconfig.html#noFallthroughCasesInSwitch).

##### Example {#example-4}

With [`noFallthroughCasesInSwitch`](/tsconfig.html#noFallthroughCasesInSwitch), this example will trigger an error:

```ts
switch (x % 2) {
case0: // Error: Fallthrough case in switch.
console.log("even");

case1:
console.log("odd");
break;
}
```

However, in the following example, no error will be reported because the fall-through case is empty:

```ts
switch (x % 3) {
case0:
case1:
console.log("Acceptable");
break;

case2:
console.log("This is *two much*!");
break;
}
```

## Function Components in React {#function-components-in-react}

TypeScript now supports [Function components](https://reactjs.org/docs/components-and-props.html#functional-and-class-components).
These are lightweight components that easily compose other components:

```ts
// Use parameter destructuring and defaults for easy definition of 'props' type
constGreeter = ({ name = "world" }) => <div>Hello, {name}!</div>;

// Properties get validated
letexample = <Greetername="TypeScript 1.8" />;
```

For this feature and simplified props, be sure to be use the [latest version of react.d.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/index.d.ts).

## Simplified `props` type management in React {#simplified-props-type-management-in-react}

In TypeScript 1.8 with the latest version of react.d.ts (see above), we’ve also greatly simplified the declaration of `props` types.

Specifically:

- You no longer need to either explicitly declare `ref` and `key` or `extend React.Props`
- The `ref` and `key` properties will appear with correct types on all components
- The `ref` property is correctly disallowed on instances of Stateless Function components

## Augmenting global/module scope from modules {#augmenting-globalmodule-scope-from-modules}

Users can now declare any augmentations that they want to make, or that any other consumers already have made, to an existing module.
Module augmentations look like plain old ambient module declarations (i.e. the `declare module "foo" { }` syntax), and are directly nested either your own modules, or in another top level ambient external module.

Furthermore, TypeScript also has the notion of *global* augmentations of the form `declare global { }`.
This allows modules to augment global types such as `Array` if necessary.

The name of a module augmentation is resolved using the same set of rules as module specifiers in `import` and `export` declarations.
The declarations in a module augmentation are merged with any existing declarations the same way they would if they were declared in the same file.

Neither module augmentations nor global augmentations can add new items to the top level scope - they can only “patch” existing declarations.

##### Example {#example-5}

Here `map.ts` can declare that it will internally patch the `Observable` type from `observable.ts` and add the `map` method to it.

```ts
// observable.ts
exportclassObservable<T> {
// ...
}
```

```ts
// map.ts
import { Observable } from"./observable";

// Create an augmentation for "./observable"
declaremodule"./observable" {

// Augment the 'Observable' class definition with interface merging
interfaceObservable<T> {
map<U>(proj: (el: T) =>U): Observable<U>;
    }

}

Observable.prototype.map = /*...*/;
```

```ts
// consumer.ts
import { Observable } from"./observable";
import"./map";

leto: Observable<number>;
o.map((x) =>x.toFixed());
```

Similarly, the global scope can be augmented from modules using a `declare global` declarations:

##### Example {#example-6}

```ts
// Ensure this is treated as a module.
export {};

declareglobal {
interfaceArray<T> {
mapToNumbers(): number[];
  }
}

Array.prototype.mapToNumbers = function () {
/* ... */
};
```

## String literal types {#string-literal-types}

It’s not uncommon for an API to expect a specific set of strings for certain values.
For instance, consider a UI library that can move elements across the screen while controlling the [“easing” of the animation.](https://wikipedia.org/wiki/Inbetweening)

```ts
declare class UIElement {
animate(options: AnimationOptions): void;
}

interfaceAnimationOptions {
deltaX: number;
deltaY: number;
easing: string; // Can be "ease-in", "ease-out", "ease-in-out"
}
```

However, this is error prone - there is nothing stopping a user from accidentally misspelling one of the valid easing values:

```ts
// No errors
newUIElement().animate({ deltaX:100, deltaY:100, easing:"ease-inout" });
```

With TypeScript 1.8, we’ve introduced string literal types.
These types are written the same way string literals are, but in type positions.

Users can now ensure that the type system will catch such errors.
Here’s our new `AnimationOptions` using string literal types:

```ts
interface AnimationOptions {
deltaX: number;
deltaY: number;
easing: "ease-in" | "ease-out" | "ease-in-out";
}

// Error: Type '"ease-inout"' is not assignable to type '"ease-in" | "ease-out" | "ease-in-out"'
newUIElement().animate({ deltaX:100, deltaY:100, easing:"ease-inout" });
```

## Improved union/intersection type inference {#improved-unionintersection-type-inference}

TypeScript 1.8 improves type inference involving source and target sides that are both union or intersection types.
For example, when inferring from `string | string[]` to `string | T`, we reduce the types to `string[]` and `T`, thus inferring `string[]` for `T`.

##### Example {#example-7}

```ts
type Maybe<T> = T | void;

functionisDefined<T>(x: Maybe<T>): xisT {
returnx !== undefined && x !== null;
}

functionisUndefined<T>(x: Maybe<T>): xisvoid {
returnx === undefined || x === null;
}

functiongetOrElse<T>(x: Maybe<T>, defaultValue: T): T {
returnisDefined(x) ? x : defaultValue;
}

functiontest1(x: Maybe<string>) {
letx1 = getOrElse(x, "Undefined"); // string
letx2 = isDefined(x) ? x : "Undefined"; // string
letx3 = isUndefined(x) ? "Undefined" : x; // string
}

functiontest2(x: Maybe<number>) {
letx1 = getOrElse(x, -1); // number
letx2 = isDefined(x) ? x : -1; // number
letx3 = isUndefined(x) ? -1 : x; // number
}
```

## Concatenate `AMD` and `System` modules with `--outFile` {#concatenate-amd-and-system-modules-with---outfile}

Specifying [`outFile`](/tsconfig.html#outFile) in conjunction with `--module amd` or `--module system` will concatenate all modules in the compilation into a single output file containing multiple module closures.

A module name will be computed for each module based on its relative location to [`rootDir`](/tsconfig.html#rootDir).

##### Example {#example-8}

```ts
// file src/a.ts
import*asBfrom"./lib/b";
exportfunctioncreateA() {
returnB.createB();
}
```

```ts
// file src/lib/b.ts
exportfunctioncreateB() {
return {};
}
```

Results in:

```js
define("lib/b", ["require", "exports"], function (require, exports) {
"use strict";
functioncreateB() {
return {};
  }
exports.createB = createB;
});
define("a", ["require", "exports", "lib/b"], function (require, exports, B) {
"use strict";
functioncreateA() {
returnB.createB();
  }
exports.createA = createA;
});
```

## Support for `default` import interop with SystemJS {#support-for-default-import-interop-with-systemjs}

Module loaders like SystemJS wrap CommonJS modules and expose then as a `default` ES6 import. This makes it impossible to share the definition files between the SystemJS and CommonJS implementation of the module as the module shape looks different based on the loader.

Setting the new compiler flag [`allowSyntheticDefaultImports`](/tsconfig.html#allowSyntheticDefaultImports) indicates that the module loader performs some kind of synthetic default import member creation not indicated in the imported .ts or .d.ts. The compiler will infer the existence of a `default` export that has the shape of the entire module itself.

System modules have this flag on by default.

## Allow captured `let`/`const` in loops {#allow-captured-letconst-in-loops}

Previously an error, now supported in TypeScript 1.8.
`let`/`const` declarations within loops and captured in functions are now emitted to correctly match `let`/`const` freshness semantics.

##### Example {#example-9}

```ts
let list = [];
for (leti = 0; i < 5; i++) {
list.push(() =>i);
}

list.forEach((f) =>console.log(f()));
```

is compiled to:

```js
var list = [];
var_loop_1 = function (i) {
list.push(function () {
returni;
  });
};
for (vari = 0; i < 5; i++) {
_loop_1(i);
}
list.forEach(function (f) {
returnconsole.log(f());
});
```

And results in

```cmd
0
1
2
3
4
```

## Improved checking for `for..in` statements {#improved-checking-for-forin-statements}

Previously the type of a `for..in` variable is inferred to `any`; that allowed the compiler to ignore invalid uses within the `for..in` body.

Starting with TypeScript 1.8:

- The type of a variable declared in a `for..in` statement is implicitly `string`.
- When an object with a numeric index signature of type `T` (such as an array) is indexed by a `for..in` variable of a containing `for..in` statement for an object *with* a numeric index signature and *without* a string index signature (again such as an array), the value produced is of type `T`.

##### Example {#example-10}

```ts
var a: MyObject[];
for (varxina) {
// Type of x is implicitly string
varobj = a[x]; // Type of obj is MyObject
}
```

## Modules are now emitted with a `"use strict";` prologue {#modules-are-now-emitted-with-a-use-strict-prologue}

Modules were always parsed in strict mode as per ES6, but for non-ES6 targets this was not respected in the generated code. Starting with TypeScript 1.8, emitted modules are always in strict mode. This shouldn’t have any visible changes in most code as TS considers most strict mode errors as errors at compile time, but it means that some things which used to silently fail at runtime in your TS code, like assigning to `NaN`, will now loudly fail. You can reference the [MDN Article](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Strict_mode) on strict mode for a detailed list of the differences between strict mode and non-strict mode.

## Including `.js` files with `--allowJs` {#including-js-files-with---allowjs}

Often there are external source files in your project that may not be authored in TypeScript.
Alternatively, you might be in the middle of converting a JS code base into TS, but still want to bundle all your JS code into a single file with the output of your new TS code.

`.js` files are now allowed as input to `tsc`.
The TypeScript compiler checks the input `.js` files for syntax errors, and emits valid output based on the [`target`](/tsconfig.html#target) and [`module`](/tsconfig.html#module) flags.
The output can be combined with other `.ts` files as well.
Source maps are still generated for `.js` files just like with `.ts` files.

## Custom JSX factories using `--reactNamespace` {#custom-jsx-factories-using---reactnamespace}

Passing `--reactNamespace <JSX factory Name>` along with `--jsx react` allows for using a different JSX factory from the default `React`.

The new factory name will be used to call `createElement` and `__spread` functions.

##### Example {#example-11}

```ts
import { jsxFactory } from "jsxFactory";

vardiv = <div>HelloJSX!</div>;
```

Compiled with:

```shell
tsc --jsx react --reactNamespace jsxFactory --m commonJS
```

Results in:

```js
"use strict";
varjsxFactory_1 = require("jsxFactory");
vardiv = jsxFactory_1.jsxFactory.createElement("div", null, "Hello JSX!");
```

## `this`-based type guards {#this-based-type-guards}

TypeScript 1.8 extends [user-defined type guard functions](/docs/handbook/release-notes/typescript-1.6.html#user-defined-type-guard-functions) to class and interface methods.

`this is T` is now valid return type annotation for methods in classes and interfaces.
When used in a type narrowing position (e.g. `if` statement), the type of the call expression target object would be narrowed to `T`.

##### Example {#example-12}

```ts
class FileSystemObject {
isFile(): thisisFile {
returnthisinstanceofFile;
  }
isDirectory(): thisisDirectory {
returnthisinstanceofDirectory;
  }
isNetworked(): thisisNetworked & this {
returnthis.networked;
  }
constructor(publicpath: string, privatenetworked: boolean) {}
}

classFileextendsFileSystemObject {
constructor(path: string, publiccontent: string) {
super(path, false);
  }
}
classDirectoryextendsFileSystemObject {
children: FileSystemObject[];
}
interfaceNetworked {
host: string;
}

letfso: FileSystemObject = newFile("foo/bar.txt", "foo");
if (fso.isFile()) {
fso.content; // fso is File
} elseif (fso.isDirectory()) {
fso.children; // fso is Directory
} elseif (fso.isNetworked()) {
fso.host; // fso is networked
}
```

## Official TypeScript NuGet package {#official-typescript-nuget-package}

Starting with TypeScript 1.8, official NuGet packages are available for the TypeScript Compiler (`tsc.exe`) as well as the MSBuild integration (`Microsoft.TypeScript.targets` and `Microsoft.TypeScript.Tasks.dll`).

Stable packages are available here:

- [Microsoft.TypeScript.Compiler](https://www.nuget.org/packages/Microsoft.TypeScript.Compiler/)
- [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/)

Also, a nightly NuGet package to match the [nightly npm package](https://web.archive.org/web/20230609112438/http://blogs.msdn.com/b/typescript/archive/2015/07/27/introducing-typescript-nightlies.aspx) is available on [myget](https://myget.org/):

- [TypeScript-Preview](https://www.myget.org/gallery/typescript-preview)

## Prettier error messages from `tsc` {#prettier-error-messages-from-tsc}

We understand that a ton of monochrome output can be a little difficult on the eyes.
Colors can help discern where a message starts and ends, and these visual clues are important when error output gets overwhelming.

By just passing the [`pretty`](/tsconfig.html#pretty) command line option, TypeScript gives more colorful output with context about where things are going wrong.

![Showing off pretty error messages in ConEmu](/assets/typescript/5.1/external/raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/pretty01.png)

## Colorization of JSX code in VS 2015 {#colorization-of-jsx-code-in-vs-2015}

With TypeScript 1.8, JSX tags are now classified and colorized in Visual Studio 2015.

![jsx](/assets/typescript/5.1/external/cloud.githubusercontent.com/assets/8052307/12271404/b875c502-b90f-11e5-93d8-c6740be354d1.png)

The classification can be further customized by changing the font and color settings for the `VB XML` color and font settings through `Tools`->`Options`->`Environment`->`Fonts and Colors` page.

## The `--project` (`-p`) flag can now take any file path {#the---project--p-flag-can-now-take-any-file-path}

The `--project` command line option originally could only take paths to a folder containing a `tsconfig.json`.
Given the different scenarios for build configurations, it made sense to allow `--project` to point to any other compatible JSON file.
For instance, a user might want to target ES2015 with CommonJS modules for Node 5, but ES5 with AMD modules for the browser.
With this new work, users can easily manage two separate build targets using `tsc` alone without having to perform hacky workarounds like placing `tsconfig.json` files in separate directories.

The old behavior still remains the same if given a directory - the compiler will try to find a file in the directory named `tsconfig.json`.

## Allow comments in tsconfig.json {#allow-comments-in-tsconfigjson}

It’s always nice to be able to document your configuration!
`tsconfig.json` now accepts single and multi-line comments.

```
{
"[compilerOptions](/tsconfig.html#compilerOptions)": {
"[target](/tsconfig.html#target)": "ES2015", // running on node v5, yaay!
"[sourceMap](/tsconfig.html#sourceMap)": true// makes debugging easier
  },
/*
   * Excluded files
   */
"[exclude](/tsconfig.html#exclude)": ["file.d.ts"]
}
```

## Support output to IPC-driven files {#support-output-to-ipc-driven-files}

TypeScript 1.8 allows users to use the [`outFile`](/tsconfig.html#outFile) argument with special file system entities like named pipes, devices, etc.

As an example, on many Unix-like systems, the standard output stream is accessible by the file `/dev/stdout`.

```shell
tsc foo.ts --outFile /dev/stdout
```

This can be used to pipe output between commands as well.

As an example, we can pipe our emitted JavaScript into a pretty printer like [pretty-js](https://www.npmjs.com/package/pretty-js):

```shell
tsc foo.ts --outFile /dev/stdout | pretty-js
```

## Improved support for `tsconfig.json` in Visual Studio 2015 {#improved-support-for-tsconfigjson-in-visual-studio-2015}

TypeScript 1.8 allows `tsconfig.json` files in all project types.
This includes ASP.NET v4 projects, *Console Application*, and the *Html Application with TypeScript* project types.
Further, you are no longer limited to a single `tsconfig.json` file but can add multiple, and each will be built as part of the project.
This allows you to separate the configuration for different parts of your application without having to use multiple different projects.

![Showing off tsconfig.json in Visual Studio](/assets/typescript/5.1/external/raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/tsconfig-in-vs.png)

We also disable the project properties page when you add a `tsconfig.json` file.
This means that all configuration changes have to be made in the `tsconfig.json` file itself.

### A couple of limitations {#a-couple-of-limitations}

- If you add a `tsconfig.json` file, TypeScript files that are not considered part of that context are not compiled.
- Apache Cordova Apps still have the existing limitation of a single `tsconfig.json` file, which must be in either the root or the `scripts` folder.
- There is no template for `tsconfig.json` in most project types.
