---
linkTitle: "JS Projects Utilizing TypeScript"
title: "TypeScript: Documentation - JS Projects Utilizing TypeScript"
description: "How to add type checking to JavaScript files using TypeScript"
weight: 1
type: docs
prev: /typescript/5.1/declaration-files/consumption
---

# JS Projects Utilizing TypeScript

The type system in TypeScript has different levels of strictness when working with a codebase:

- A type-system based only on inference with JavaScript code
- Incremental typing in JavaScript [via JSDoc](/typescript/5.1/javascript/jsdoc-supported-types)
- Using `// @ts-check` in a JavaScript file
- TypeScript code
- TypeScript with [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict) enabled

Each step represents a move towards a safer type-system, but not every project needs that level of verification.

## TypeScript with JavaScript {#typescript-with-javascript}

This is when you use an editor which uses TypeScript to provide tooling like auto-complete, jump to symbol and refactoring tools like rename.
The [homepage ↗](https://www.typescriptlang.org/) has a list of editors which have TypeScript plugins.

## Providing Type Hints in JS via JSDoc {#providing-type-hints-in-js-via-jsdoc}

In a `.js` file, types can often be inferred. When types can’t be inferred, they can be specified using JSDoc syntax.

JSDoc annotations come before a declaration will be used to set the type of that declaration. For example:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCm4DeA7ArgWwEaIE4C+4IwAUAG4CG+4AHgNxll3gC84ADA+MMOAHkA0i3bgAZlQA2AZ0Q8+goQH4AhEA)

```js
/** @type {number} */
var x;
 
x = 0; // OK
x = false; // OK?!
```

You can find the full list of supported JSDoc patterns [in JSDoc Supported Types](/typescript/5.1/javascript/jsdoc-supported-types).

## `@ts-check` {#ts-check}

The last line of the previous code sample would raise an error in TypeScript, but it doesn’t by default in a JS project.
To enable errors in your JavaScript files add: `// @ts-check` to the first line in your `.js` files to have TypeScript raise it as an error.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEBcGcFoGMAWBTeBrAUCCyBOuB7XaALlACYBmc8rAKjokgE8AHZUAbwDsBXAWwBGeAL6g6wDADcAhrlAAPANwYMC0AF5QABiWhsAeQDSazaABmMgDbRke7ADkCkUMaA)

```js
// @ts-check
/** @type {number} */
var x;
 
x = 0; // OK
x = false; // Not OK
```

```text {filename="Generated error"}
Type 'boolean' is not assignable to type 'number'.
```

If you have a lot of JavaScript files you want to add errors to then you can switch to using a [`jsconfig.json`](/typescript/5.1/project-configuration/tsconfig-json).
You can skip checking some files by adding a `// @ts-nocheck` comment to files.

TypeScript may offer you errors which you disagree with, in those cases you can ignore errors on specific lines by adding `// @ts-ignore` or `// @ts-expect-error` on the preceding line.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEBcGcFoGMAWBTeBrAUMAVNikBPAB2VAG8A7AVwFsAjZAJwF9RtgMA3AQ0dAA8A3Bgz9QAXlAAGQaBCgA8gGksYKHGT8S8SLCaMA9o1ETQAM24AbaMlnyAcgciKlQA)

```js
// @ts-check
/** @type {number} */
var x;
 
x = 0; // OK
// @ts-expect-error
x = false; // Not OK
```

To learn more about how JavaScript is interpreted by TypeScript read [How TS Type Checks JS](/typescript/5.1/javascript/type-checking-javascript-files)
