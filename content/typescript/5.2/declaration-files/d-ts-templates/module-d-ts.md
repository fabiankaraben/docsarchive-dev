---
linkTitle: "Modules .d.ts"
title: "TypeScript: Documentation - Modules .d.ts"
description: ""
weight: 1
type: docs
prev: /typescript/5.2/declaration-files/library-structures
---

# Modules .d.ts

## Comparing JavaScript to an example DTS {#comparing-javascript-to-an-example-dts}

## Common CommonJS Patterns {#common-commonjs-patterns}

A module using CommonJS patterns uses `module.exports` to describe the exported values. For example, here is a module which exports a function and a numerical constant:

```js
const maxInterval = 12;

functiongetArrayLength(arr) {
returnarr.length;
}

module.exports = {
getArrayLength,
maxInterval,
};
```

This can be described by the following `.d.ts`:

```ts
export function getArrayLength(arr: any[]): number;
exportconstmaxInterval: 12;
```

The TypeScript playground can show you the `.d.ts` equivalent for JavaScript code. You can [try it yourself here ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABAcwKZQIICcsEMCeAMqmMlABYAUuOAlIgN6IBQiiW6IWSNWAdABsSZcswC+zCAgDOURAFtcADwAq5GKUQBeRAEYATM2by4AExBC+qJQAc4WKNO2NWKdNjxFhFADSvFquqk4sxAA).

The `.d.ts` syntax intentionally looks like [ES Modules ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) syntax.
ES Modules was ratified by TC39 in 2015 as part of ES2015 (ES6), while it has been available via transpilers for a long time, however if you have a JavaScript codebase using ES Modules:

```js
export function getArrayLength(arr) {
returnarr.length;
}
```

This would have the following `.d.ts` equivalent:

```ts
export function getArrayLength(arr: any[]): number;
```

### Default Exports {#default-exports}

In CommonJS you can export any value as the default export, for example here is a regular expression module:

```js
module.exports = /hello( world)?/;
```

Which can be described by the following .d.ts:

```ts
declare const helloWorld: RegExp;
exportdefaulthelloWorld;
```

Or a number:

```js
module.exports = 3.142;
```

```ts
declare const pi: number;
exportdefaultpi;
```

One style of exporting in CommonJS is to export a function.
Because a function is also an object, then extra fields can be added and are included in the export.

```js
function getArrayLength(arr) {
returnarr.length;
}
getArrayLength.maxInterval = 12;

module.exports = getArrayLength;
```

Which can be described with:

```ts
export default function getArrayLength(arr: any[]): number;
exportconstmaxInterval: 12;
```

Note that using `export default` in your .d.ts files requires [`esModuleInterop: true` ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop) to work.
If you can’t have `esModuleInterop: true` in your project, such as when you’re submitting a PR to Definitely Typed, you’ll have to use the `export=` syntax instead. This older syntax is harder to use but works everywhere.
Here’s how the above example would have to be written using `export=`:

```ts
declare function getArrayLength(arr: any[]): number;
declarenamespacegetArrayLength {
declareconstmaxInterval: 12;
}

export = getArrayLength;
```

See [Module: Functions](/typescript/5.2/declaration-files/d-ts-templates/module-function-d-ts) for details of how that works, and the [Modules reference ↗](https://www.typescriptlang.org/docs/handbook/modules.html) page.

## Handling Many Consuming Import {#handling-many-consuming-import}

There are many ways to import a module in modern consuming code:

```ts
const fastify = require("fastify");
const { fastify } = require("fastify");
importfastify = require("fastify");
import*asFastifyfrom"fastify";
import { fastify, FastifyInstance } from"fastify";
importfastifyfrom"fastify";
importfastify, { FastifyInstance } from"fastify";
```

Covering all of these cases requires the JavaScript code to actually support all of these patterns.
To support many of these patterns, a CommonJS module would need to look something like:

```js
class FastifyInstance {}

functionfastify() {
returnnewFastifyInstance();
}

fastify.FastifyInstance = FastifyInstance;

// Allows for { fastify }
fastify.fastify = fastify;
// Allows for strict ES Module support
fastify.default = fastify;
// Sets the default export
module.exports = fastify;
```

## Types in Modules {#types-in-modules}

You may want to provide a type for JavaScript code which does not exist

```js
function getArrayMetadata(arr) {
return {
length:getArrayLength(arr),
firstObject:arr[0],
  };
}

module.exports = {
getArrayMetadata,
};
```

This can be described with:

```ts
export type ArrayMetadata = {
length: number;
firstObject: any | undefined;
};
exportfunctiongetArrayMetadata(arr: any[]): ArrayMetadata;
```

This example is a good case for [using generics ↗](https://www.typescriptlang.org/docs/handbook/generics.html#generic-types) to provide richer type information:

```ts
export type ArrayMetadata<ArrType> = {
length: number;
firstObject: ArrType | undefined;
};

exportfunctiongetArrayMetadata<ArrType>(
arr: ArrType[]
): ArrayMetadata<ArrType>;
```

Now the type of the array propagates into the `ArrayMetadata` type.

The types which are exported can then be re-used by consumers of the modules using either `import` or `import type` in TypeScript code or [JSDoc imports](/typescript/5.2/javascript/jsdoc-supported-types#import-types).

### Namespaces in Module Code {#namespaces-in-module-code}

Trying to describe the runtime relationship of JavaScript code can be tricky.
When the ES Module-like syntax doesn’t provide enough tools to describe the exports then you can use `namespaces`.

For example, you may have complex enough types to describe that you choose to namespace them inside your `.d.ts`:

```ts
// This represents the JavaScript class which would be available at runtime
exportclassAPI {
constructor(baseURL: string);
getInfo(opts: API.InfoRequest): API.InfoResponse;
}

// This namespace is merged with the API class and allows for consumers, and this file
// to have types which are nested away in their own sections.
declarenamespaceAPI {
exportinterfaceInfoRequest {
id: string;
  }

exportinterfaceInfoResponse {
width: number;
height: number;
  }
}
```

To understand how namespaces work in `.d.ts` files read the [`.d.ts` deep dive](/typescript/5.2/declaration-files/deep-dive).

### Optional Global Usage {#optional-global-usage}

You can use `export as namespace` to declare that your module will be available in the global scope in UMD contexts:

```ts
export as namespace moduleName;
```

## Reference Example {#reference-example}

To give you an idea of how all these pieces can come together, here is a reference `.d.ts` to start with when making a new module

```ts
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>

/*~ This is the module template file. You should rename it to index.d.ts
 *~ and place it in a folder with the same name as the module.
 *~ For example, if you were writing a file for "super-greeter", this
 *~ file should be 'super-greeter/index.d.ts'
 */

/*~ If this module is a UMD module that exposes a global variable 'myLib' when
 *~ loaded outside a module loader environment, declare that global here.
 *~ Otherwise, delete this declaration.
 */
exportasnamespacemyLib;

/*~ If this module exports functions, declare them like so.
 */
exportfunctionmyFunction(a: string): string;
exportfunctionmyOtherFunction(a: number): number;

/*~ You can declare types that are available via importing the module */
exportinterfaceSomeType {
name: string;
length: number;
extras?: string[];
}

/*~ You can declare properties of the module using const, let, or var */
exportconstmyField: number;
```

### Library file layout {#library-file-layout}

The layout of your declaration files should mirror the layout of the library.

A library can consist of multiple modules, such as

```
myLib
  +---- index.js
  +---- foo.js
  +---- bar
         +---- index.js
         +---- baz.js
```

These could be imported as

```js
var a = require("myLib");
varb = require("myLib/foo");
varc = require("myLib/bar");
vard = require("myLib/bar/baz");
```

Your declaration files should thus be

```
@types/myLib
  +---- index.d.ts
  +---- foo.d.ts
  +---- bar
         +---- index.d.ts
         +---- baz.d.ts
```

### Testing your types {#testing-your-types}

If you are planning on submitting these changes to DefinitelyTyped for everyone to also use, then we recommend you:

> 1. Create a new folder in `node_modules/@types/[libname]`
> 2. Create an `index.d.ts` in that folder, and copy the example in
> 3. See where your usage of the module breaks, and start to fill out the index.d.ts
> 4. When you’re happy, clone [DefinitelyTyped/DefinitelyTyped ↗](https://github.com/DefinitelyTyped) and follow the instructions in the README.
> 

Otherwise

> 1. Create a new file in the root of your source tree: `[libname].d.ts`
> 2. Add `declare module "[libname]" {  }`
> 3. Add the template inside the braces of the declare module, and see where your usage breaks
> 
