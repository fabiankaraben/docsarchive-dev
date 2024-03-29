---
linkTitle: "Using Babel with TypeScript"
title: "TypeScript: Documentation - Using Babel with TypeScript"
description: "How to create a hybrid Babel + TypeScript project"
weight: 5
type: docs
next: /typescript/5.1/whats-new/typescript-5-1
canonical: /typescript/5.2/tutorials/babel-with-typescript
---

# Using Babel with TypeScript

## Babel vs `tsc` for TypeScript {#babel-vs-tsc-for-typescript}

When making a modern JavaScript project, you might ask yourself what is the right way to convert files from TypeScript to JavaScript?

A lot of the time the answer is *“it depends”*, or *“someone may have decided for you”* depending on the project. If you are building your project with an existing framework like [tsdx ↗](https://tsdx.io/), [Angular ↗](https://angular.io/), [NestJS ↗](https://nestjs.com/) or any framework mentioned in the [Getting Started ↗](https://www.typescriptlang.org/docs/home) then this decision is handled for you.

However, a useful heuristic could be:

- Is your build output mostly the same as your source input files? Use `tsc`
- Do you need a build pipeline with multiple potential outputs? Use `babel` for transpiling and `tsc` for type checking

## Babel for transpiling, `tsc` for types {#babel-for-transpiling-tsc-for-types}

This is a common pattern for projects with existing build infrastructure which may have been ported from a JavaScript codebase to TypeScript.

This technique is a hybrid approach, using Babel’s [preset-typescript ↗](https://babeljs.io/docs/en/babel-preset-typescript) to generate your JS files, and then using TypeScript to do type checking and `.d.ts` file generation.

By using babel’s support for TypeScript, you get the ability to work with existing build pipelines and are more likely to have a faster JS emit time because Babel does not type check your code.

#### Type Checking and d.ts file generation {#type-checking-and-dts-file-generation}

The downside to using babel is that you don’t get type checking during the transition from TS to JS. This can mean that type errors which you miss in your editor could sneak through into production code.

In addition to that, Babel cannot create `.d.ts` files for your TypeScript which can make it harder to work with your project if it is a library.

To fix these issues, you would probably want to set up a command to type check your project using TSC. This likely means duplicating some of your babel config into a corresponding [`tsconfig.json` ↗](https://www.typescriptlang.org/tsconfig.html) and ensuring these flags are enabled:

```
"compilerOptions": {
// Ensure that .d.ts files are created by tsc, but not .js files
"[declaration ↗](https://www.typescriptlang.org/tsconfig.html#declaration)": true,
"[emitDeclarationOnly ↗](https://www.typescriptlang.org/tsconfig.html#emitDeclarationOnly)": true,
// Ensure that Babel can safely transpile files in the TypeScript project
"[isolatedModules ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules)": true
}
```

For more information on these flags:

- [`isolatedModules` ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules)
- [`declaration` ↗](https://www.typescriptlang.org/tsconfig.html#declaration), [`emitDeclarationOnly` ↗](https://www.typescriptlang.org/tsconfig.html#emitDeclarationOnly)
