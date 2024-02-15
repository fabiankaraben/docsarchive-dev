---
linkTitle: "tsc CLI Options"
title: "TypeScript: Documentation - tsc CLI Options"
description: "A very high-level overview of the CLI compiler options for tsc"
weight: 4
type: docs
---

# tsc CLI Options

## Using the CLI {#using-the-cli}

Running `tsc` locally will compile the closest project defined by a `tsconfig.json`, or you can compile a set of TypeScript
files by passing in a glob of files you want. When input files are specified on the command line, `tsconfig.json` files are
ignored.

```sh
# Run a compile based on a backwards look through the fs for a tsconfig.json
tsc

# Emit JS for just the index.ts with the compiler defaults
tsc index.ts

# Emit JS for any .ts files in the folder src, with the default settings
tsc src/*.ts

# Emit files referenced in with the compiler settings from tsconfig.production.json
tsc --project tsconfig.production.json

# Emit d.ts files for a js file with showing compiler options which are booleans
tsc index.js --declaration --emitDeclarationOnly

# Emit a single .js file from two files via compiler options which take string arguments
tsc app.ts util.ts --target esnext --outfile index.js
```

## Compiler Options {#compiler-options}

**If you’re looking for more information about the compiler options in a tsconfig, check out the [TSConfig Reference](/typescript/5.2/project-configuration/tsconfig)**

### CLI Commands

|Flag|Type|
|---|---|
|`--all`|`boolean`<br>|
|Show all compiler options.<br>|
|`--generateTrace`|`string`<br>|
|Generates an event trace and a list of types.<br>|
|`--help`|`boolean`<br>|
|Gives local information for help on the CLI.<br>|
|`--init`|`boolean`<br>|
|Initializes a TypeScript project and creates a tsconfig.json file.<br>|
|`--listFilesOnly`|`boolean`<br>|
|Print names of files that are part of the compilation and then stop processing.<br>|
|`--locale`|`string`<br>|
|Set the language of the messaging from TypeScript. This does not affect emit.<br>|
|`--project`|`string`<br>|
|Compile the project given the path to its configuration file, or to a folder with a 'tsconfig.json'.<br>|
|`--showConfig`|`boolean`<br>|
|Print the final configuration instead of building.<br>|
|`--version`|`boolean`<br>|
|Print the compiler's version.<br>|


### Build Options

|Flag|Type|
|---|---|
|`--build`|`boolean`<br>|
|Build one or more projects and their dependencies, if out of date<br>|
|`--clean`|`boolean`<br>|
|Delete the outputs of all projects.<br>|
|`--dry`|`boolean`<br>|
|Show what would be built (or deleted, if specified with '--clean')<br>|
|`[--force ↗](https://www.typescriptlang.org/tsconfig.html#force)`|`boolean`<br>|
|Build all projects, including those that appear to be up to date.<br>|
|`[--verbose ↗](https://www.typescriptlang.org/tsconfig.html#verbose)`|`boolean`<br>|
|Enable verbose logging.<br>|


### Watch Options

|Flag|Type|
|---|---|
|`[--excludeDirectories ↗](https://www.typescriptlang.org/tsconfig.html#excludeDirectories)`|`list`<br>|
|Remove a list of directories from the watch process.<br>|
|`[--excludeFiles ↗](https://www.typescriptlang.org/tsconfig.html#excludeFiles)`|`list`<br>|
|Remove a list of files from the watch mode's processing.<br>|
|`[--fallbackPolling ↗](https://www.typescriptlang.org/tsconfig.html#fallbackPolling)`|`fixedinterval`, `priorityinterval`, `dynamicpriority`, or `fixedchunksize`<br>|
|Specify what approach the watcher should use if the system runs out of native file watchers.<br>|
|`[--synchronousWatchDirectory ↗](https://www.typescriptlang.org/tsconfig.html#synchronousWatchDirectory)`|`boolean`<br>|
|Synchronously call callbacks and update the state of directory watchers on platforms that don`t support recursive watching natively.<br>|
|`--watch`|`boolean`<br>|
|Watch input files.<br>|
|`[--watchDirectory ↗](https://www.typescriptlang.org/tsconfig.html#watchDirectory)`|`usefsevents`, `fixedpollinginterval`, `dynamicprioritypolling`, or `fixedchunksizepolling`<br>|
|Specify how directories are watched on systems that lack recursive file-watching functionality.<br>|
|`[--watchFile ↗](https://www.typescriptlang.org/tsconfig.html#watchFile)`|`fixedpollinginterval`, `prioritypollinginterval`, `dynamicprioritypolling`, `fixedchunksizepolling`, `usefsevents`, or `usefseventsonparentdirectory`<br>|
|Specify how the TypeScript watch mode works.<br>|


### Compiler Flags

|Flag|Type|Default|
|---|---|---|
|`[--allowArbitraryExtensions ↗](https://www.typescriptlang.org/tsconfig.html#allowArbitraryExtensions)`|`boolean`<br>|`false`<br>|
|Enable importing files with any extension, provided a declaration file is present.<br>|
|`[--allowImportingTsExtensions ↗](https://www.typescriptlang.org/tsconfig.html#allowImportingTsExtensions)`|`boolean`<br>|`false`<br>|
|Allow imports to include TypeScript file extensions.<br>|
|`[--allowJs ↗](https://www.typescriptlang.org/tsconfig.html#allowJs)`|`boolean`<br>|`false`<br>|
|Allow JavaScript files to be a part of your program. Use the `checkJS` option to get errors from these files.<br>|
|`[--allowSyntheticDefaultImports ↗](https://www.typescriptlang.org/tsconfig.html#allowSyntheticDefaultImports)`|`boolean`<br>|`true` if [`esModuleInterop` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#esModuleInterop) is enabled, [`module` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#module) is `system`, or [`moduleResolution` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#module-resolution) is `bundler`; `false` otherwise.<br>|
|Allow 'import x from y' when a module doesn't have a default export.<br>|
|`[--allowUmdGlobalAccess ↗](https://www.typescriptlang.org/tsconfig.html#allowUmdGlobalAccess)`|`boolean`<br>|`false`<br>|
|Allow accessing UMD globals from modules.<br>|
|`[--allowUnreachableCode ↗](https://www.typescriptlang.org/tsconfig.html#allowUnreachableCode)`|`boolean`<br>||
|Disable error reporting for unreachable code.<br>|
|`[--allowUnusedLabels ↗](https://www.typescriptlang.org/tsconfig.html#allowUnusedLabels)`|`boolean`<br>||
|Disable error reporting for unused labels.<br>|
|`[--alwaysStrict ↗](https://www.typescriptlang.org/tsconfig.html#alwaysStrict)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Ensure 'use strict' is always emitted.<br>|
|`[--assumeChangesOnlyAffectDirectDependencies ↗](https://www.typescriptlang.org/tsconfig.html#assumeChangesOnlyAffectDirectDependencies)`|`boolean`<br>|`false`<br>|
|Have recompiles in projects that use [`incremental` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#incremental) and `watch` mode assume that changes within a file will only affect files directly depending on it.<br>|
|`[--baseUrl ↗](https://www.typescriptlang.org/tsconfig.html#baseUrl)`|`string`<br>||
|Specify the base directory to resolve bare specifier module names.<br>|
|`[--charset ↗](https://www.typescriptlang.org/tsconfig.html#charset)`|`string`<br>|`utf8`<br>|
|No longer supported. In early versions, manually set the text encoding for reading files.<br>|
|`[--checkJs ↗](https://www.typescriptlang.org/tsconfig.html#checkJs)`|`boolean`<br>|`false`<br>|
|Enable error reporting in type-checked JavaScript files.<br>|
|`[--composite ↗](https://www.typescriptlang.org/tsconfig.html#composite)`|`boolean`<br>|`false`<br>|
|Enable constraints that allow a TypeScript project to be used with project references.<br>|
|`[--customConditions ↗](https://www.typescriptlang.org/tsconfig.html#customConditions)`|`list`<br>||
|Conditions to set in addition to the resolver-specific defaults when resolving imports.<br>|
|`[--declaration ↗](https://www.typescriptlang.org/tsconfig.html#declaration)`|`boolean`<br>|`true` if [`composite` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#composite); `false` otherwise.<br>|
|Generate .d.ts files from TypeScript and JavaScript files in your project.<br>|
|`[--declarationDir ↗](https://www.typescriptlang.org/tsconfig.html#declarationDir)`|`string`<br>||
|Specify the output directory for generated declaration files.<br>|
|`[--declarationMap ↗](https://www.typescriptlang.org/tsconfig.html#declarationMap)`|`boolean`<br>|`false`<br>|
|Create sourcemaps for d.ts files.<br>|
|`[--diagnostics ↗](https://www.typescriptlang.org/tsconfig.html#diagnostics)`|`boolean`<br>|`false`<br>|
|Output compiler performance information after building.<br>|
|`[--disableReferencedProjectLoad ↗](https://www.typescriptlang.org/tsconfig.html#disableReferencedProjectLoad)`|`boolean`<br>|`false`<br>|
|Reduce the number of projects loaded automatically by TypeScript.<br>|
|`[--disableSizeLimit ↗](https://www.typescriptlang.org/tsconfig.html#disableSizeLimit)`|`boolean`<br>|`false`<br>|
|Remove the 20mb cap on total source code size for JavaScript files in the TypeScript language server.<br>|
|`[--disableSolutionSearching ↗](https://www.typescriptlang.org/tsconfig.html#disableSolutionSearching)`|`boolean`<br>|`false`<br>|
|Opt a project out of multi-project reference checking when editing.<br>|
|`[--disableSourceOfProjectReferenceRedirect ↗](https://www.typescriptlang.org/tsconfig.html#disableSourceOfProjectReferenceRedirect)`|`boolean`<br>|`false`<br>|
|Disable preferring source files instead of declaration files when referencing composite projects.<br>|
|`[--downlevelIteration ↗](https://www.typescriptlang.org/tsconfig.html#downlevelIteration)`|`boolean`<br>|`false`<br>|
|Emit more compliant, but verbose and less performant JavaScript for iteration.<br>|
|`[--emitBOM ↗](https://www.typescriptlang.org/tsconfig.html#emitBOM)`|`boolean`<br>|`false`<br>|
|Emit a UTF-8 Byte Order Mark (BOM) in the beginning of output files.<br>|
|`[--emitDeclarationOnly ↗](https://www.typescriptlang.org/tsconfig.html#emitDeclarationOnly)`|`boolean`<br>|`false`<br>|
|Only output d.ts files and not JavaScript files.<br>|
|`[--emitDecoratorMetadata ↗](https://www.typescriptlang.org/tsconfig.html#emitDecoratorMetadata)`|`boolean`<br>|`false`<br>|
|Emit design-type metadata for decorated declarations in source files.<br>|
|`[--esModuleInterop ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop)`|`boolean`<br>|`true` if [`module` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#module) is `node16` or `nodenext`; `false` otherwise.<br>|
|Emit additional JavaScript to ease support for importing CommonJS modules. This enables [`allowSyntheticDefaultImports` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#allowSyntheticDefaultImports) for type compatibility.<br>|
|`[--exactOptionalPropertyTypes ↗](https://www.typescriptlang.org/tsconfig.html#exactOptionalPropertyTypes)`|`boolean`<br>|`false`<br>|
|Interpret optional property types as written, rather than adding `undefined`.<br>|
|`[--experimentalDecorators ↗](https://www.typescriptlang.org/tsconfig.html#experimentalDecorators)`|`boolean`<br>|`false`<br>|
|Enable experimental support for TC39 stage 2 draft decorators.<br>|
|`[--explainFiles ↗](https://www.typescriptlang.org/tsconfig.html#explainFiles)`|`boolean`<br>|`false`<br>|
|Print files read during the compilation including why it was included.<br>|
|`[--extendedDiagnostics ↗](https://www.typescriptlang.org/tsconfig.html#extendedDiagnostics)`|`boolean`<br>|`false`<br>|
|Output more detailed compiler performance information after building.<br>|
|`[--forceConsistentCasingInFileNames ↗](https://www.typescriptlang.org/tsconfig.html#forceConsistentCasingInFileNames)`|`boolean`<br>|`true`<br>|
|Ensure that casing is correct in imports.<br>|
|`[--generateCpuProfile ↗](https://www.typescriptlang.org/tsconfig.html#generateCpuProfile)`|`string`<br>|`profile.cpuprofile`<br>|
|Emit a v8 CPU profile of the compiler run for debugging.<br>|
|`[--importHelpers ↗](https://www.typescriptlang.org/tsconfig.html#importHelpers)`|`boolean`<br>|`false`<br>|
|Allow importing helper functions from tslib once per project, instead of including them per-file.<br>|
|`[--importsNotUsedAsValues ↗](https://www.typescriptlang.org/tsconfig.html#importsNotUsedAsValues)`|`remove`, `preserve`, or `error`<br>|`remove`<br>|
|Specify emit/checking behavior for imports that are only used for types.<br>|
|`[--incremental ↗](https://www.typescriptlang.org/tsconfig.html#incremental)`|`boolean`<br>|`true` if [`composite` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#composite); `false` otherwise.<br>|
|Save .tsbuildinfo files to allow for incremental compilation of projects.<br>|
|`[--inlineSourceMap ↗](https://www.typescriptlang.org/tsconfig.html#inlineSourceMap)`|`boolean`<br>|`false`<br>|
|Include sourcemap files inside the emitted JavaScript.<br>|
|`[--inlineSources ↗](https://www.typescriptlang.org/tsconfig.html#inlineSources)`|`boolean`<br>|`false`<br>|
|Include source code in the sourcemaps inside the emitted JavaScript.<br>|
|`[--isolatedModules ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules)`|`boolean`<br>|`false`<br>|
|Ensure that each file can be safely transpiled without relying on other imports.<br>|
|`[--jsx ↗](https://www.typescriptlang.org/tsconfig.html#jsx)`|`preserve`, `react`, `react-native`, `react-jsx`, or `react-jsxdev`<br>||
|Specify what JSX code is generated.<br>|
|`[--jsxFactory ↗](https://www.typescriptlang.org/tsconfig.html#jsxFactory)`|`string`<br>|`React.createElement`<br>|
|Specify the JSX factory function used when targeting React JSX emit, e.g. 'React.createElement' or 'h'.<br>|
|`[--jsxFragmentFactory ↗](https://www.typescriptlang.org/tsconfig.html#jsxFragmentFactory)`|`string`<br>|`React.Fragment`<br>|
|Specify the JSX Fragment reference used for fragments when targeting React JSX emit e.g. 'React.Fragment' or 'Fragment'.<br>|
|`[--jsxImportSource ↗](https://www.typescriptlang.org/tsconfig.html#jsxImportSource)`|`string`<br>|`react`<br>|
|Specify module specifier used to import the JSX factory functions when using `jsx: react-jsx*`.<br>|
|`[--keyofStringsOnly ↗](https://www.typescriptlang.org/tsconfig.html#keyofStringsOnly)`|`boolean`<br>|`false`<br>|
|Make keyof only return strings instead of string, numbers or symbols. Legacy option.<br>|
|`[--lib ↗](https://www.typescriptlang.org/tsconfig.html#lib)`|`list`<br>||
|Specify a set of bundled library declaration files that describe the target runtime environment.<br>|
|`[--listEmittedFiles ↗](https://www.typescriptlang.org/tsconfig.html#listEmittedFiles)`|`boolean`<br>|`false`<br>|
|Print the names of emitted files after a compilation.<br>|
|`[--listFiles ↗](https://www.typescriptlang.org/tsconfig.html#listFiles)`|`boolean`<br>|`false`<br>|
|Print all of the files read during the compilation.<br>|
|`[--mapRoot ↗](https://www.typescriptlang.org/tsconfig.html#mapRoot)`|`string`<br>||
|Specify the location where debugger should locate map files instead of generated locations.<br>|
|`[--maxNodeModuleJsDepth ↗](https://www.typescriptlang.org/tsconfig.html#maxNodeModuleJsDepth)`|`number`<br>|`0`<br>|
|Specify the maximum folder depth used for checking JavaScript files from `node_modules`. Only applicable with [`allowJs` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#allowJs).<br>|
|`[--module ↗](https://www.typescriptlang.org/tsconfig.html#module)`|`none`, `commonjs`, `amd`, `umd`, `system`, `es6`/`es2015`, `es2020`, `es2022`, `esnext`, `node16`, or `nodenext`<br>|`CommonJS` if [`target` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#target) is `ES3` or `ES5`; `ES6`/`ES2015` otherwise.<br>|
|Specify what module code is generated.<br>|
|`[--moduleDetection ↗](https://www.typescriptlang.org/tsconfig.html#moduleDetection)`|`legacy`, `auto`, or `force`<br>|"auto": Treat files with imports, exports, import.meta, jsx (with jsx: react-jsx), or esm format (with module: node16+) as modules.<br>|
|Specify what method is used to detect whether a file is a script or a module.<br>|
|`[--moduleResolution ↗](https://www.typescriptlang.org/tsconfig.html#moduleResolution)`|`classic`, `node10`/`node`, `node16`, `nodenext`, or `bundler`<br>|`Classic` if [`module` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#module) is `AMD`, `UMD`, `System`, or `ES6`/`ES2015`; Matches if [`module` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#module) is `node16` or `nodenext`; `Node` otherwise.<br>|
|Specify how TypeScript looks up a file from a given module specifier.<br>|
|`[--moduleSuffixes ↗](https://www.typescriptlang.org/tsconfig.html#moduleSuffixes)`|`list`<br>||
|List of file name suffixes to search when resolving a module.<br>|
|`[--newLine ↗](https://www.typescriptlang.org/tsconfig.html#newLine)`|`crlf` or `lf`<br>|Platform specific.<br>|
|Set the newline character for emitting files.<br>|
|`[--noEmit ↗](https://www.typescriptlang.org/tsconfig.html#noEmit)`|`boolean`<br>|`false`<br>|
|Disable emitting files from a compilation.<br>|
|`[--noEmitHelpers ↗](https://www.typescriptlang.org/tsconfig.html#noEmitHelpers)`|`boolean`<br>|`false`<br>|
|Disable generating custom helper functions like `__extends` in compiled output.<br>|
|`[--noEmitOnError ↗](https://www.typescriptlang.org/tsconfig.html#noEmitOnError)`|`boolean`<br>|`false`<br>|
|Disable emitting files if any type checking errors are reported.<br>|
|`[--noErrorTruncation ↗](https://www.typescriptlang.org/tsconfig.html#noErrorTruncation)`|`boolean`<br>|`false`<br>|
|Disable truncating types in error messages.<br>|
|`[--noFallthroughCasesInSwitch ↗](https://www.typescriptlang.org/tsconfig.html#noFallthroughCasesInSwitch)`|`boolean`<br>|`false`<br>|
|Enable error reporting for fallthrough cases in switch statements.<br>|
|`[--noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Enable error reporting for expressions and declarations with an implied `any` type.<br>|
|`[--noImplicitOverride ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitOverride)`|`boolean`<br>|`false`<br>|
|Ensure overriding members in derived classes are marked with an override modifier.<br>|
|`[--noImplicitReturns ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitReturns)`|`boolean`<br>|`false`<br>|
|Enable error reporting for codepaths that do not explicitly return in a function.<br>|
|`[--noImplicitThis ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitThis)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Enable error reporting when `this` is given the type `any`.<br>|
|`[--noImplicitUseStrict ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitUseStrict)`|`boolean`<br>|`false`<br>|
|Disable adding 'use strict' directives in emitted JavaScript files.<br>|
|`[--noLib ↗](https://www.typescriptlang.org/tsconfig.html#noLib)`|`boolean`<br>|`false`<br>|
|Disable including any library files, including the default lib.d.ts.<br>|
|`[--noPropertyAccessFromIndexSignature ↗](https://www.typescriptlang.org/tsconfig.html#noPropertyAccessFromIndexSignature)`|`boolean`<br>|`false`<br>|
|Enforces using indexed accessors for keys declared using an indexed type.<br>|
|`[--noResolve ↗](https://www.typescriptlang.org/tsconfig.html#noResolve)`|`boolean`<br>|`false`<br>|
|Disallow `import`s, `require`s or `<reference>`s from expanding the number of files TypeScript should add to a project.<br>|
|`[--noStrictGenericChecks ↗](https://www.typescriptlang.org/tsconfig.html#noStrictGenericChecks)`|`boolean`<br>|`false`<br>|
|Disable strict checking of generic signatures in function types.<br>|
|`[--noUncheckedIndexedAccess ↗](https://www.typescriptlang.org/tsconfig.html#noUncheckedIndexedAccess)`|`boolean`<br>|`false`<br>|
|Add `undefined` to a type when accessed using an index.<br>|
|`[--noUnusedLocals ↗](https://www.typescriptlang.org/tsconfig.html#noUnusedLocals)`|`boolean`<br>|`false`<br>|
|Enable error reporting when local variables aren't read.<br>|
|`[--noUnusedParameters ↗](https://www.typescriptlang.org/tsconfig.html#noUnusedParameters)`|`boolean`<br>|`false`<br>|
|Raise an error when a function parameter isn't read.<br>|
|`[--out ↗](https://www.typescriptlang.org/tsconfig.html#out)`|`string`<br>||
|Deprecated setting. Use [`outFile` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#outFile) instead.<br>|
|`[--outDir ↗](https://www.typescriptlang.org/tsconfig.html#outDir)`|`string`<br>||
|Specify an output folder for all emitted files.<br>|
|`[--outFile ↗](https://www.typescriptlang.org/tsconfig.html#outFile)`|`string`<br>||
|Specify a file that bundles all outputs into one JavaScript file. If [`declaration` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#declaration) is true, also designates a file that bundles all .d.ts output.<br>|
|`[--paths ↗](https://www.typescriptlang.org/tsconfig.html#paths)`|`object`<br>||
|Specify a set of entries that re-map imports to additional lookup locations.<br>|
|`[--plugins ↗](https://www.typescriptlang.org/tsconfig.html#plugins)`|`list`<br>||
|Specify a list of language service plugins to include.<br>|
|`[--preserveConstEnums ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums)`|`boolean`<br>|`true` if [`isolatedModules` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#isolatedModules); `false` otherwise.<br>|
|Disable erasing `const enum` declarations in generated code.<br>|
|`[--preserveSymlinks ↗](https://www.typescriptlang.org/tsconfig.html#preserveSymlinks)`|`boolean`<br>|`false`<br>|
|Disable resolving symlinks to their realpath. This correlates to the same flag in node.<br>|
|`[--preserveValueImports ↗](https://www.typescriptlang.org/tsconfig.html#preserveValueImports)`|`boolean`<br>|`false`<br>|
|Preserve unused imported values in the JavaScript output that would otherwise be removed.<br>|
|`[--preserveWatchOutput ↗](https://www.typescriptlang.org/tsconfig.html#preserveWatchOutput)`|`boolean`<br>|`false`<br>|
|Disable wiping the console in watch mode.<br>|
|`[--pretty ↗](https://www.typescriptlang.org/tsconfig.html#pretty)`|`boolean`<br>|`true`<br>|
|Enable color and formatting in TypeScript's output to make compiler errors easier to read.<br>|
|`[--reactNamespace ↗](https://www.typescriptlang.org/tsconfig.html#reactNamespace)`|`string`<br>|`React`<br>|
|Specify the object invoked for `createElement`. This only applies when targeting `react` JSX emit.<br>|
|`[--removeComments ↗](https://www.typescriptlang.org/tsconfig.html#removeComments)`|`boolean`<br>|`false`<br>|
|Disable emitting comments.<br>|
|`[--resolveJsonModule ↗](https://www.typescriptlang.org/tsconfig.html#resolveJsonModule)`|`boolean`<br>|`false`<br>|
|Enable importing .json files.<br>|
|`[--resolvePackageJsonExports ↗](https://www.typescriptlang.org/tsconfig.html#resolvePackageJsonExports)`|`boolean`<br>|`true` when [`moduleResolution` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#moduleResolution) is `node16`, `nodenext`, or `bundler`; otherwise `false`<br>|
|Use the package.json 'exports' field when resolving package imports.<br>|
|`[--resolvePackageJsonImports ↗](https://www.typescriptlang.org/tsconfig.html#resolvePackageJsonImports)`|`boolean`<br>|`true` when [`moduleResolution` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#moduleResolution) is `node16`, `nodenext`, or `bundler`; otherwise `false`<br>|
|Use the package.json 'imports' field when resolving imports.<br>|
|`[--rootDir ↗](https://www.typescriptlang.org/tsconfig.html#rootDir)`|`string`<br>|Computed from the list of input files.<br>|
|Specify the root folder within your source files.<br>|
|`[--rootDirs ↗](https://www.typescriptlang.org/tsconfig.html#rootDirs)`|`list`<br>|Computed from the list of input files.<br>|
|Allow multiple folders to be treated as one when resolving modules.<br>|
|`[--skipDefaultLibCheck ↗](https://www.typescriptlang.org/tsconfig.html#skipDefaultLibCheck)`|`boolean`<br>|`false`<br>|
|Skip type checking .d.ts files that are included with TypeScript.<br>|
|`[--skipLibCheck ↗](https://www.typescriptlang.org/tsconfig.html#skipLibCheck)`|`boolean`<br>|`false`<br>|
|Skip type checking all .d.ts files.<br>|
|`[--sourceMap ↗](https://www.typescriptlang.org/tsconfig.html#sourceMap)`|`boolean`<br>|`false`<br>|
|Create source map files for emitted JavaScript files.<br>|
|`[--sourceRoot ↗](https://www.typescriptlang.org/tsconfig.html#sourceRoot)`|`string`<br>||
|Specify the root path for debuggers to find the reference source code.<br>|
|`[--strict ↗](https://www.typescriptlang.org/tsconfig.html#strict)`|`boolean`<br>|`false`<br>|
|Enable all strict type-checking options.<br>|
|`[--strictBindCallApply ↗](https://www.typescriptlang.org/tsconfig.html#strictBindCallApply)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Check that the arguments for `bind`, `call`, and `apply` methods match the original function.<br>|
|`[--strictFunctionTypes ↗](https://www.typescriptlang.org/tsconfig.html#strictFunctionTypes)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|When assigning functions, check to ensure parameters and the return values are subtype-compatible.<br>|
|`[--strictNullChecks ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|When type checking, take into account `null` and `undefined`.<br>|
|`[--strictPropertyInitialization ↗](https://www.typescriptlang.org/tsconfig.html#strictPropertyInitialization)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Check for class properties that are declared but not set in the constructor.<br>|
|`[--stripInternal ↗](https://www.typescriptlang.org/tsconfig.html#stripInternal)`|`boolean`<br>|`false`<br>|
|Disable emitting declarations that have `@internal` in their JSDoc comments.<br>|
|`[--suppressExcessPropertyErrors ↗](https://www.typescriptlang.org/tsconfig.html#suppressExcessPropertyErrors)`|`boolean`<br>|`false`<br>|
|Disable reporting of excess property errors during the creation of object literals.<br>|
|`[--suppressImplicitAnyIndexErrors ↗](https://www.typescriptlang.org/tsconfig.html#suppressImplicitAnyIndexErrors)`|`boolean`<br>|`false`<br>|
|Suppress [`noImplicitAny` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#noImplicitAny) errors when indexing objects that lack index signatures.<br>|
|`[--target ↗](https://www.typescriptlang.org/tsconfig.html#target)`|`es3`, `es5`, `es6`/`es2015`, `es2016`, `es2017`, `es2018`, `es2019`, `es2020`, `es2021`, `es2022`, or `esnext`<br>|`ES3`<br>|
|Set the JavaScript language version for emitted JavaScript and include compatible library declarations.<br>|
|`[--traceResolution ↗](https://www.typescriptlang.org/tsconfig.html#traceResolution)`|`boolean`<br>|`false`<br>|
|Log paths used during the [`moduleResolution` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#moduleResolution) process.<br>|
|`[--tsBuildInfoFile ↗](https://www.typescriptlang.org/tsconfig.html#tsBuildInfoFile)`|`string`<br>|`.tsbuildinfo`<br>|
|The file to store `.tsbuildinfo` incremental build information in.<br>|
|`[--typeRoots ↗](https://www.typescriptlang.org/tsconfig.html#typeRoots)`|`list`<br>||
|Specify multiple folders that act like `./node_modules/@types`.<br>|
|`[--types ↗](https://www.typescriptlang.org/tsconfig.html#types)`|`list`<br>||
|Specify type package names to be included without being referenced in a source file.<br>|
|`[--useDefineForClassFields ↗](https://www.typescriptlang.org/tsconfig.html#useDefineForClassFields)`|`boolean`<br>|`true` if [`target` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#target) is `ES2022` or higher, including `ESNext`; `false` otherwise.<br>|
|Emit ECMAScript-standard-compliant class fields.<br>|
|`[--useUnknownInCatchVariables ↗](https://www.typescriptlang.org/tsconfig.html#useUnknownInCatchVariables)`|`boolean`<br>|`true` if [`strict` ↗](https://www.typescriptlang.org/docs/handbook/compiler-options.html#strict); `false` otherwise.<br>|
|Default catch clause variables as `unknown` instead of `any`.<br>|
|`[--verbatimModuleSyntax ↗](https://www.typescriptlang.org/tsconfig.html#verbatimModuleSyntax)`|`boolean`<br>|`false`<br>|
|Do not transform or elide any imports or exports not marked as type-only, ensuring they are written in the output file's format based on the 'module' setting.<br>|


## Related {#related}

- Every option is fully explained in the [TSConfig Reference](/typescript/5.2/project-configuration/tsconfig).
- Learn how to use a [`tsconfig.json`](/typescript/5.2/project-configuration/tsconfig-json) file.
- Learn how to work in an [MSBuild project](/typescript/5.2/project-configuration/compiler-options-in-msbuild).
