---
linkTitle: "Modules"
title: "TypeScript: Documentation - Modules"
description: "How modules work in TypeScript"
weight: 10
type: docs
---

# Modules

#export).

Conversely, to consume a variable, function, class, interface, etc. exported from a different module, it has to be imported using one of the [`import` forms](/typescript/5.1/reference/modules#import).

Modules are declarative; the relationships between modules are specified in terms of imports and exports at the file level.

Modules import one another using a module loader.
At runtime the module loader is responsible for locating and executing all dependencies of a module before executing it.
Well-known module loaders used in JavaScript are Node.js’s loader for [CommonJS ↗](https://wikipedia.org/wiki/CommonJS) modules and the [RequireJS ↗](https://web.archive.org/web/20230825151643/http://requirejs.org/) loader for [AMD ↗](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) modules in Web applications.

In TypeScript, just as in ECMAScript 2015, any file containing a top-level `import` or `export` is considered a module.
Conversely, a file without any top-level `import` or `export` declarations is treated as a script whose contents are available in the global scope (and therefore to modules as well).

## Export {#export}

### Exporting a declaration {#exporting-a-declaration}

Any declaration (such as a variable, function, class, type alias, or interface) can be exported by adding the `export` keyword.

##### StringValidator.ts {#stringvalidatorts}

```ts
export interface StringValidator {
isAcceptable(s: string): boolean;
}
```

##### ZipCodeValidator.ts {#zipcodevalidatorts}

```ts
import { StringValidator } from "./StringValidator";

exportconstnumberRegexp = /^[0-9]+$/;

exportclassZipCodeValidatorimplementsStringValidator {
isAcceptable(s: string) {
returns.length === 5 && numberRegexp.test(s);
  }
}
```

### Export statements {#export-statements}

Export statements are handy when exports need to be renamed for consumers, so the above example can be written as:

```ts
class ZipCodeValidator implements StringValidator {
isAcceptable(s: string) {
returns.length === 5 && numberRegexp.test(s);
  }
}
export { ZipCodeValidator };
export { ZipCodeValidatorasmainValidator };
```

### Re-exports {#re-exports}

Often modules extend other modules, and partially expose some of their features.
A re-export does not import it locally, or introduce a local variable.

##### ParseIntBasedZipCodeValidator.ts {#parseintbasedzipcodevalidatorts}

```ts
export class ParseIntBasedZipCodeValidator {
isAcceptable(s: string) {
returns.length === 5 && parseInt(s).toString() === s;
  }
}

// Export original validator but rename it
export { ZipCodeValidatorasRegExpBasedZipCodeValidator } from"./ZipCodeValidator";
```

Optionally, a module can wrap one or more modules and combine all their exports using `export * from "module"` syntax.

##### AllValidators.ts {#allvalidatorsts}

```ts
export * from "./StringValidator"; // exports 'StringValidator' interface
export*from"./ZipCodeValidator"; // exports 'ZipCodeValidator' class and 'numberRegexp' constant value
export*from"./ParseIntBasedZipCodeValidator"; //  exports the 'ParseIntBasedZipCodeValidator' class
// and re-exports 'RegExpBasedZipCodeValidator' as alias
// of the 'ZipCodeValidator' class from 'ZipCodeValidator.ts'
// module.
```

## Import {#import}

Importing is just about as easy as exporting from a module.
Importing an exported declaration is done through using one of the `import` forms below:

### Import a single export from a module {#import-a-single-export-from-a-module}

```ts
import { ZipCodeValidator } from "./ZipCodeValidator";

letmyValidator = newZipCodeValidator();
```

imports can also be renamed

```ts
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
letmyValidator = newZCV();
```

### Import the entire module into a single variable, and use it to access the module exports {#import-the-entire-module-into-a-single-variable-and-use-it-to-access-the-module-exports}

```ts
import * as validator from "./ZipCodeValidator";
letmyValidator = newvalidator.ZipCodeValidator();
```

### Import a module for side-effects only {#import-a-module-for-side-effects-only}

Though not recommended practice, some modules set up some global state that can be used by other modules.
These modules may not have any exports, or the consumer is not interested in any of their exports.
To import these modules, use:

```ts
import "./my-module.js";
```

### Importing Types {#importing-types}

Prior to TypeScript 3.8, you can import a type using `import`.
With TypeScript 3.8, you can import a type using the `import` statement, or using `import type`.

```ts
// Re-using the same import
import { APIResponseType } from"./api";

// Explicitly use import type
importtype { APIResponseType } from"./api";

// Explicitly pull out a value (getResponse) and a type (APIResponseType) 
import { getResponse, typeAPIResponseType} from"./api";
```

Any explicitly marked `type` import is guaranteed to be removed from your JavaScript, and tools like Babel can make better assumptions about your code via the [`isolatedModules` ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules) compiler flag.
You can read more in the [3.8 release notes ↗](https://devblogs.microsoft.com/typescript/announcing-typescript-3-8-beta/#type-only-imports-exports).

With TypeScript 4.5, you can use a `type` modifier on individual named imports.

```ts
import { someFunc, type BaseType } from "./some-module.js";
```

## Default exports {#default-exports}

Each module can optionally export a `default` export.
Default exports are marked with the keyword `default`; and there can only be one `default` export per module.
`default` exports are imported using a different import form.

`default` exports are really handy.
For instance, a library like jQuery might have a default export of `jQuery` or `$`, which we’d probably also import under the name `$` or `jQuery`.

##### [JQuery.d.ts ↗](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/jquery/JQuery.d.ts) {#jquerydts}

```ts
declare let $: JQuery;
exportdefault$;
```

##### App.ts {#appts}

```ts
import $ from "jquery";

$("button.continue").html("Next Step...");
```

Classes and function declarations can be authored directly as default exports.
Default export class and function declaration names are optional.

##### ZipCodeValidator.ts {#zipcodevalidatorts-1}

```ts
export default class ZipCodeValidator {
staticnumberRegexp = /^[0-9]+$/;
isAcceptable(s: string) {
returns.length === 5 && ZipCodeValidator.numberRegexp.test(s);
  }
}
```

##### Test.ts {#testts}

```ts
import validator from "./ZipCodeValidator";

letmyValidator = newvalidator();
```

or

##### StaticZipCodeValidator.ts {#staticzipcodevalidatorts}

```ts
const numberRegexp = /^[0-9]+$/;

exportdefaultfunction (s: string) {
returns.length === 5 && numberRegexp.test(s);
}
```

##### Test.ts {#testts-1}

```ts
import validate from "./StaticZipCodeValidator";

letstrings = ["Hello", "98052", "101"];

// Use function validate
strings.forEach((s) => {
console.log(`"${s}" ${validate(s) ?"matches":"does not match"}`);
});
```

`default` exports can also be just values:

##### OneTwoThree.ts {#onetwothreets}

```ts
export default "123";
```

##### Log.ts {#logts}

```ts
import num from "./OneTwoThree";

console.log(num); // "123"
```

## Export all as x {#export-all-as-x}

With TypeScript 3.8, you can use `export * as ns` as a shorthand for re-exporting another module with a name:

```ts
export * as utilities from "./utilities";
```

This takes all of the dependencies from a module and makes it an exported field, you could import it like this:

```ts
import { utilities } from "./index";
```

## `export =` and `import = require()` {#export--and-import--require}

Both CommonJS and AMD generally have the concept of an `exports` object which contains all exports from a module.

They also support replacing the `exports` object with a custom single object.
Default exports are meant to act as a replacement for this behavior; however, the two are incompatible.
TypeScript supports `export =` to model the traditional CommonJS and AMD workflow.

The `export =` syntax specifies a single object that is exported from the module.
This can be a class, interface, namespace, function, or enum.

##### ZipCodeValidator.ts {#zipcodevalidatorts-2}

```ts
let numberRegexp = /^[0-9]+$/;
classZipCodeValidator {
isAcceptable(s: string) {
returns.length === 5 && numberRegexp.test(s);
  }
}
export = ZipCodeValidator;
```

##### Test.ts {#testts-2}

```ts
import zip = require("./ZipCodeValidator");

// Some samples to try
letstrings = ["Hello", "98052", "101"];

// Validators to use
letvalidator = newzip();

// Show whether each string passed each validator
strings.forEach((s) => {
console.log(
`"${s}" - ${validator.isAcceptable(s) ?"matches":"does not match"}`
  );
});
```

## Code Generation for Modules {#code-generation-for-modules}

Depending on the module target specified during compilation, the compiler will generate appropriate code for Node.js ([CommonJS ↗](https://web.archive.org/web/20230825151643/http://wiki.commonjs.org/wiki/CommonJS)), require.js ([AMD ↗](https://github.com/amdjs/amdjs-api/wiki/AMD)), [UMD ↗](https://github.com/umdjs/umd), [SystemJS ↗](https://github.com/systemjs/systemjs), or [ECMAScript 2015 native modules ↗](https://web.archive.org/web/20230825151643/http://www.ecma-international.org/ecma-262/6.0/#sec-modules) (ES6) module-loading systems.
For more information on what the `define`, `require` and `register` calls in the generated code do, consult the documentation for each module loader.

This simple example shows how the names used during importing and exporting get translated into the module loading code.

##### SimpleModule.ts {#simplemodulets}

```ts
import m = require("mod");
exportlett = m.something + 1;
```

##### AMD / RequireJS SimpleModule.js {#amd--requirejs-simplemodulejs}

```js
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
exports.t = mod_1.something + 1;
});
```

##### CommonJS / Node SimpleModule.js {#commonjs--node-simplemodulejs}

```js
var mod_1 = require("./mod");
exports.t = mod_1.something + 1;
```

##### UMD SimpleModule.js {#umd-simplemodulejs}

```js
(function (factory) {
if (typeofmodule === "object" && typeofmodule.exports === "object") {
varv = factory(require, exports);
if (v !== undefined) module.exports = v;
  } elseif (typeofdefine === "function" && define.amd) {
define(["require", "exports", "./mod"], factory);
  }
})(function (require, exports) {
varmod_1 = require("./mod");
exports.t = mod_1.something + 1;
});
```

##### System SimpleModule.js {#system-simplemodulejs}

```js
System.register(["./mod"], function (exports_1) {
varmod_1;
vart;
return {
setters: [
function (mod_1_1) {
mod_1 = mod_1_1;
      },
    ],
execute:function () {
exports_1("t", (t = mod_1.something + 1));
    },
  };
});
```

##### Native ECMAScript 2015 modules SimpleModule.js {#native-ecmascript-2015-modules-simplemodulejs}

```js
import { something } from "./mod";
exportvart = something + 1;
```

## Simple Example {#simple-example}

Below, we’ve consolidated the Validator implementations used in previous examples to only export a single named export from each module.

To compile, we must specify a module target on the command line. For Node.js, use `--module commonjs`;
for require.js, use `--module amd`. For example:

```
tsc --module commonjs Test.ts
```

When compiled, each module will become a separate `.js` file.
As with reference tags, the compiler will follow `import` statements to compile dependent files.

##### Validation.ts {#validationts}

```ts
export interface StringValidator {
isAcceptable(s: string): boolean;
}
```

##### LettersOnlyValidator.ts {#lettersonlyvalidatorts}

```ts
import { StringValidator } from "./Validation";

constlettersRegexp = /^[A-Za-z]+$/;

exportclassLettersOnlyValidatorimplementsStringValidator {
isAcceptable(s: string) {
returnlettersRegexp.test(s);
  }
}
```

##### ZipCodeValidator.ts {#zipcodevalidatorts-3}

```ts
import { StringValidator } from "./Validation";

constnumberRegexp = /^[0-9]+$/;

exportclassZipCodeValidatorimplementsStringValidator {
isAcceptable(s: string) {
returns.length === 5 && numberRegexp.test(s);
  }
}
```

##### Test.ts {#testts-3}

```ts
import { StringValidator } from "./Validation";
import { ZipCodeValidator } from"./ZipCodeValidator";
import { LettersOnlyValidator } from"./LettersOnlyValidator";

// Some samples to try
letstrings = ["Hello", "98052", "101"];

// Validators to use
letvalidators: { [s: string]: StringValidator } = {};
validators["ZIP code"] = newZipCodeValidator();
validators["Letters only"] = newLettersOnlyValidator();

// Show whether each string passed each validator
strings.forEach((s) => {
for (letnameinvalidators) {
console.log(
`"${s}" - ${
validators[name].isAcceptable(s) ?"matches":"does not match"
}${name}`
    );
  }
});
```

## Optional Module Loading and Other Advanced Loading Scenarios {#optional-module-loading-and-other-advanced-loading-scenarios}

In some cases, you may want to only load a module under some conditions.
In TypeScript, we can use the pattern shown below to implement this and other advanced loading scenarios to directly invoke the module loaders without losing type safety.

The compiler detects whether each module is used in the emitted JavaScript.
If a module identifier is only ever used as part of a type annotations and never as an expression, then no `require` call is emitted for that module.
This elision of unused references is a good performance optimization, and also allows for optional loading of those modules.

The core idea of the pattern is that the `import id = require("...")` statement gives us access to the types exposed by the module.
The module loader is invoked (through `require`) dynamically, as shown in the `if` blocks below.
This leverages the reference-elision optimization so that the module is only loaded when needed.
For this pattern to work, it’s important that the symbol defined via an `import` is only used in type positions (i.e. never in a position that would be emitted into the JavaScript).

To maintain type safety, we can use the `typeof` keyword.
The `typeof` keyword, when used in a type position, produces the type of a value, in this case the type of the module.

##### Dynamic Module Loading in Node.js {#dynamic-module-loading-in-nodejs}

```ts
declare function require(moduleName: string): any;

import { ZipCodeValidatorasZip } from"./ZipCodeValidator";

if (needZipValidation) {
letZipCodeValidator: typeofZip = require("./ZipCodeValidator");
letvalidator = newZipCodeValidator();
if (validator.isAcceptable("...")) {
/* ... */
  }
}
```

##### Sample: Dynamic Module Loading in require.js {#sample-dynamic-module-loading-in-requirejs}

```ts
declare function require(
moduleNames: string[],
onLoad: (...args: any[]) =>void
): void;

import*asZipfrom"./ZipCodeValidator";

if (needZipValidation) {
require(["./ZipCodeValidator"], (ZipCodeValidator: typeofZip) => {
letvalidator = newZipCodeValidator.ZipCodeValidator();
if (validator.isAcceptable("...")) {
/* ... */
    }
  });
}
```

##### Sample: Dynamic Module Loading in System.js {#sample-dynamic-module-loading-in-systemjs}

```ts
declare const System: any;

import { ZipCodeValidatorasZip } from"./ZipCodeValidator";

if (needZipValidation) {
System.import("./ZipCodeValidator").then((ZipCodeValidator: typeofZip) => {
varx = newZipCodeValidator();
if (x.isAcceptable("...")) {
/* ... */
    }
  });
}
```

## Working with Other JavaScript Libraries {#working-with-other-javascript-libraries}

To describe the shape of libraries not written in TypeScript, we need to declare the API that the library exposes.

We call declarations that don’t define an implementation “ambient”.
Typically, these are defined in `.d.ts` files.
If you’re familiar with C/C++, you can think of these as `.h` files.
Let’s look at a few examples.

### Ambient Modules {#ambient-modules}

In Node.js, most tasks are accomplished by loading one or more modules.
We could define each module in its own `.d.ts` file with top-level export declarations, but it’s more convenient to write them as one larger `.d.ts` file.
To do so, we use a construct similar to ambient namespaces, but we use the `module` keyword and the quoted name of the module which will be available to a later import.
For example:

##### node.d.ts (simplified excerpt) {#nodedts-simplified-excerpt}

```ts
declare module "url" {
exportinterfaceUrl {
protocol?: string;
hostname?: string;
pathname?: string;
  }

exportfunctionparse(
urlStr: string,
parseQueryString?,
slashesDenoteHost?
  ): Url;
}

declaremodule"path" {
exportfunctionnormalize(p: string): string;
exportfunctionjoin(...paths: any[]): string;
exportvarsep: string;
}
```

Now we can `/// <reference>``node.d.ts` and then load the modules using `import url = require("url");` or `import * as URL from "url"`.

```ts
/// <reference path="node.d.ts"/>
import*asURLfrom"url";
letmyUrl = URL.parse("https://www.typescriptlang.org");
```

#### Shorthand ambient modules {#shorthand-ambient-modules}

If you don’t want to take the time to write out declarations before using a new module, you can use a shorthand declaration to get started quickly.

##### declarations.d.ts {#declarationsdts}

```ts
declare module "hot-new-module";
```

All imports from a shorthand module will have the `any` type.

```ts
import x, { y } from "hot-new-module";
x(y);
```

#### Wildcard module declarations {#wildcard-module-declarations}

Some module loaders such as [SystemJS ↗](https://github.com/systemjs/systemjs/blob/master/docs/module-types.md)
and [AMD ↗](https://github.com/amdjs/amdjs-api/blob/master/LoaderPlugins.md) allow non-JavaScript content to be imported.
These typically use a prefix or suffix to indicate the special loading semantics.
Wildcard module declarations can be used to cover these cases.

```ts
declare module "*!text" {
constcontent: string;
exportdefaultcontent;
}
// Some do it the other way around.
declaremodule"json!*" {
constvalue: any;
exportdefaultvalue;
}
```

Now you can import things that match `"*!text"` or `"json!*"`.

```ts
import fileContent from "./xyz.txt!text";
importdatafrom"json!http://example.com/data.json";
console.log(data, fileContent);
```

#### UMD modules {#umd-modules}

Some libraries are designed to be used in many module loaders, or with no module loading (global variables).
These are known as [UMD ↗](https://github.com/umdjs/umd) modules.
These libraries can be accessed through either an import or a global variable.
For example:

##### math-lib.d.ts {#math-libdts}

```ts
export function isPrime(x: number): boolean;
exportasnamespacemathLib;
```

The library can then be used as an import within modules:

```ts
import { isPrime } from "math-lib";
isPrime(2);
mathLib.isPrime(2); // ERROR: can't use the global definition from inside a module
```

It can also be used as a global variable, but only inside of a script.
(A script is a file with no imports or exports.)

```ts
mathLib.isPrime(2);
```

## Guidance for structuring modules {#guidance-for-structuring-modules}

### Export as close to top-level as possible {#export-as-close-to-top-level-as-possible}

Consumers of your module should have as little friction as possible when using things that you export.
Adding too many levels of nesting tends to be cumbersome, so think carefully about how you want to structure things.

Exporting a namespace from your module is an example of adding too many layers of nesting.
While namespaces sometime have their uses, they add an extra level of indirection when using modules.
This can quickly become a pain point for users, and is usually unnecessary.

Static methods on an exported class have a similar problem - the class itself adds a layer of nesting.
Unless it increases expressivity or intent in a clearly useful way, consider simply exporting a helper function.

#### If you’re only exporting a single `class` or `function`, use `export default` {#if-youre-only-exporting-a-single-class-or-function-use-export-default}

Just as “exporting near the top-level” reduces friction on your module’s consumers, so does introducing a default export.
If a module’s primary purpose is to house one specific export, then you should consider exporting it as a default export.
This makes both importing and actually using the import a little easier.
For example:

##### MyClass.ts {#myclassts}

```ts
export default class SomeType {
constructor() { ... }
}
```

##### MyFunc.ts {#myfuncts}

```ts
export default function getThing() {
return"thing";
}
```

##### Consumer.ts {#consumerts}

```ts
import t from "./MyClass";
importffrom"./MyFunc";
letx = newt();
console.log(f());
```

This is optimal for consumers. They can name your type whatever they want (`t` in this case) and don’t have to do any excessive dotting to find your objects.

#### If you’re exporting multiple objects, put them all at top-level {#if-youre-exporting-multiple-objects-put-them-all-at-top-level}

##### MyThings.ts {#mythingsts}

```ts
export class SomeType {
/* ... */
}
exportfunctionsomeFunc() {
/* ... */
}
```

Conversely when importing:

#### Explicitly list imported names {#explicitly-list-imported-names}

##### Consumer.ts {#consumerts-1}

```ts
import { SomeType, someFunc } from "./MyThings";
letx = newSomeType();
lety = someFunc();
```

#### Use the namespace import pattern if you’re importing a large number of things {#use-the-namespace-import-pattern-if-youre-importing-a-large-number-of-things}

##### MyLargeModule.ts {#mylargemodulets}

```ts
export class Dog { ... }
exportclassCat { ... }
exportclassTree { ... }
exportclassFlower { ... }
```

##### Consumer.ts {#consumerts-2}

```ts
import * as myLargeModule from "./MyLargeModule.ts";
letx = newmyLargeModule.Dog();
```

### Re-export to extend {#re-export-to-extend}

Often you will need to extend functionality on a module.
A common JS pattern is to augment the original object with *extensions*, similar to how JQuery extensions work.
As we’ve mentioned before, modules do not *merge* like global namespace objects would.
The recommended solution is to *not* mutate the original object, but rather export a new entity that provides the new functionality.

Consider a simple calculator implementation defined in module `Calculator.ts`.
The module also exports a helper function to test the calculator functionality by passing a list of input strings and writing the result at the end.

#### Calculator.ts {#calculatorts}

```ts
export class Calculator {
privatecurrent = 0;
privatememory = 0;
privateoperator: string;

protectedprocessDigit(digit: string, currentValue: number) {
if (digit >= "0" && digit <= "9") {
returncurrentValue * 10 + (digit.charCodeAt(0) - "0".charCodeAt(0));
    }
  }

protectedprocessOperator(operator: string) {
if (["+", "-", "*", "/"].indexOf(operator) >= 0) {
returnoperator;
    }
  }

protectedevaluateOperator(
operator: string,
left: number,
right: number
  ): number {
switch (this.operator) {
case"+":
returnleft + right;
case"-":
returnleft - right;
case"*":
returnleft * right;
case"/":
returnleft / right;
    }
  }

privateevaluate() {
if (this.operator) {
this.memory = this.evaluateOperator(
this.operator,
this.memory,
this.current
      );
    } else {
this.memory = this.current;
    }
this.current = 0;
  }

publichandleChar(char: string) {
if (char === "=") {
this.evaluate();
return;
    } else {
letvalue = this.processDigit(char, this.current);
if (value !== undefined) {
this.current = value;
return;
      } else {
letvalue = this.processOperator(char);
if (value !== undefined) {
this.evaluate();
this.operator = value;
return;
        }
      }
    }
thrownewError(`Unsupported input: '${char}'`);
  }

publicgetResult() {
returnthis.memory;
  }
}

exportfunctiontest(c: Calculator, input: string) {
for (leti = 0; i < input.length; i++) {
c.handleChar(input[i]);
  }

console.log(`result of '${input}' is '${c.getResult()}'`);
}
```

Here is a simple test for the calculator using the exposed `test` function.

#### TestCalculator.ts {#testcalculatorts}

```ts
import { Calculator, test } from "./Calculator";

letc = newCalculator();
test(c, "1+2*33/11="); // prints 9
```

Now to extend this to add support for input with numbers in bases other than 10, let’s create `ProgrammerCalculator.ts`

#### ProgrammerCalculator.ts {#programmercalculatorts}

```ts
import { Calculator } from "./Calculator";

classProgrammerCalculatorextendsCalculator {
staticdigits = [
"0",
"1",
"2",
"3",
"4",
"5",
"6",
"7",
"8",
"9",
"A",
"B",
"C",
"D",
"E",
"F",
  ];

constructor(publicbase: number) {
super();
constmaxBase = ProgrammerCalculator.digits.length;
if (base <= 0 || base > maxBase) {
thrownewError(`base has to be within 0 to ${maxBase} inclusive.`);
    }
  }

protectedprocessDigit(digit: string, currentValue: number) {
if (ProgrammerCalculator.digits.indexOf(digit) >= 0) {
return (
currentValue * this.base + ProgrammerCalculator.digits.indexOf(digit)
      );
    }
  }
}

// Export the new extended calculator as Calculator
export { ProgrammerCalculatorasCalculator };

// Also, export the helper function
export { test } from"./Calculator";
```

The new module `ProgrammerCalculator` exports an API shape similar to that of the original `Calculator` module, but does not augment any objects in the original module.
Here is a test for our ProgrammerCalculator class:

#### TestProgrammerCalculator.ts {#testprogrammercalculatorts}

```ts
import { Calculator, test } from "./ProgrammerCalculator";

letc = newCalculator(2);
test(c, "001+010="); // prints 3
```

### Do not use namespaces in modules {#do-not-use-namespaces-in-modules}

When first moving to a module-based organization, a common tendency is to wrap exports in an additional layer of namespaces.
Modules have their own scope, and only exported declarations are visible from outside the module.
With this in mind, namespace provide very little, if any, value when working with modules.

On the organization front, namespaces are handy for grouping together logically-related objects and types in the global scope.
For example, in C#, you’re going to find all the collection types in System.Collections.
By organizing our types into hierarchical namespaces, we provide a good “discovery” experience for users of those types.
Modules, on the other hand, are already present in a file system, necessarily.
We have to resolve them by path and filename, so there’s a logical organization scheme for us to use.
We can have a /collections/generic/ folder with a list module in it.

Namespaces are important to avoid naming collisions in the global scope.
For example, you might have `My.Application.Customer.AddForm` and `My.Application.Order.AddForm` — two types with the same name, but a different namespace.
This, however, is not an issue with modules.
Within a module, there’s no plausible reason to have two objects with the same name.
From the consumption side, the consumer of any given module gets to pick the name that they will use to refer to the module, so accidental naming conflicts are impossible.

> For more discussion about modules and namespaces see [Namespaces and Modules](/typescript/5.1/reference/namespaces-and-modules).
> 

### Red Flags {#red-flags}

All of the following are red flags for module structuring. Double-check that you’re not trying to namespace your external modules if any of these apply to your files:

- A file whose only top-level declaration is `export namespace Foo { ... }` (remove `Foo` and move everything ‘up’ a level)
- Multiple files that have the same `export namespace Foo {` at top-level (don’t think that these are going to combine into one `Foo`!)
