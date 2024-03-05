---
linkTitle: "Compiler Options in MSBuild"
title: "TypeScript: Documentation - Compiler Options in MSBuild"
description: "Which compiler options are available in MSBuild projects."
weight: 2
type: docs
canonical: /typescript/5.2/project-configuration/compiler-options-in-msbuild
---

# Compiler Options in MSBuild

## Overview {#overview}

When you have an MSBuild based project which utilizes TypeScript such as an ASP.NET Core project, you can configure TypeScript in two ways. Either via a `tsconfig.json` or via the project settings.

## Using a `tsconfig.json` {#using-a-tsconfigjson}

We recommend using a `tsconfig.json` for your project when possible. To add one to an existing project, add a new item to your project which is called a “TypeScript JSON Configuration File” in modern versions of Visual Studio.

The new `tsconfig.json` will then be used as the source of truth for TypeScript-specific build information like files and configuration. You can learn [about how TSConfigs works here](/typescript/5.1/project-configuration/tsconfig-json) and there is a [comprehensive reference here ↗](https://www.typescriptlang.org/tsconfig.html).

## Using Project Settings {#using-project-settings}

You can also define the configuration for TypeScript inside you project’s settings. This is done by editing the XML in your `.csproj` to define `PropertyGroups` which describe how the build can work:

```xml
<PropertyGroup>
<TypeScriptNoEmitOnError>true</TypeScriptNoEmitOnError>
<TypeScriptNoImplicitReturns>true</TypeScriptNoImplicitReturns>
</PropertyGroup>
```

There is a series of mappings for common TypeScript settings, these are settings which map directly to [TypeScript cli options](/typescript/5.1/project-configuration/compiler-options) and are used to help you write a more understandable project file. You can use the [TSConfig reference ↗](https://www.typescriptlang.org/tsconfig.html) to get more information on what values and defaults are for each mapping.

### CLI Mappings

|MSBuild Config Name|TSC Flag|
|---|---|
|`<TypeScriptAllowJS>`|`[--allowJs ↗](https://www.typescriptlang.org/tsconfig.html#allowJs)`|
|Allow JavaScript files to be a part of your program. Use the `checkJS` option to get errors from these files.<br>|
|`<TypeScriptRemoveComments>`|`[--removeComments ↗](https://www.typescriptlang.org/tsconfig.html#removeComments)`|
|Disable emitting comments.<br>|
|`<TypeScriptNoImplicitAny>`|`[--noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)`|
|Enable error reporting for expressions and declarations with an implied `any` type..<br>|
|`<TypeScriptGeneratesDeclarations>`|`[--declaration ↗](https://www.typescriptlang.org/tsconfig.html#declaration)`|
|Generate .d.ts files from TypeScript and JavaScript files in your project.<br>|
|`<TypeScriptModuleKind>`|`[--module ↗](https://www.typescriptlang.org/tsconfig.html#module)`|
|Specify what module code is generated.<br>|
|`<TypeScriptJSXEmit>`|`[--jsx ↗](https://www.typescriptlang.org/tsconfig.html#jsx)`|
|Specify what JSX code is generated.<br>|
|`<TypeScriptOutDir>`|`[--outDir ↗](https://www.typescriptlang.org/tsconfig.html#outDir)`|
|Specify an output folder for all emitted files.<br>|
|`<TypeScriptSourceMap>`|`[--sourcemap ↗](https://www.typescriptlang.org/tsconfig.html#sourcemap)`|
|Create source map files for emitted JavaScript files.<br>|
|`<TypeScriptTarget>`|`[--target ↗](https://www.typescriptlang.org/tsconfig.html#target)`|
|Set the JavaScript language version for emitted JavaScript and include compatible library declarations.<br>|
|`<TypeScriptNoResolve>`|`[--noResolve ↗](https://www.typescriptlang.org/tsconfig.html#noResolve)`|
|Disallow `import`s, `require`s or `<reference>`s from expanding the number of files TypeScript should add to a project.<br>|
|`<TypeScriptMapRoot>`|`[--mapRoot ↗](https://www.typescriptlang.org/tsconfig.html#mapRoot)`|
|Specify the location where debugger should locate map files instead of generated locations.<br>|
|`<TypeScriptSourceRoot>`|`[--sourceRoot ↗](https://www.typescriptlang.org/tsconfig.html#sourceRoot)`|
|Specify the root path for debuggers to find the reference source code.<br>|
|`<TypeScriptCharset>`|`[--charset ↗](https://www.typescriptlang.org/tsconfig.html#charset)`|
|No longer supported. In early versions, manually set the text encoding for reading files.<br>|
|`<TypeScriptEmitBOM>`|`[--emitBOM ↗](https://www.typescriptlang.org/tsconfig.html#emitBOM)`|
|Emit a UTF-8 Byte Order Mark (BOM) in the beginning of output files.<br>|
|`<TypeScriptNoLib>`|`[--noLib ↗](https://www.typescriptlang.org/tsconfig.html#noLib)`|
|Disable including any library files, including the default lib.d.ts.<br>|
|`<TypeScriptPreserveConstEnums>`|`[--preserveConstEnums ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums)`|
|Disable erasing `const enum` declarations in generated code.<br>|
|`<TypeScriptSuppressImplicitAnyIndexErrors>`|`[--suppressImplicitAnyIndexErrors ↗](https://www.typescriptlang.org/tsconfig.html#suppressImplicitAnyIndexErrors)`|
|Suppress `noImplicitAny` errors when indexing objects that lack index signatures.<br>|
|`<TypeScriptNoEmitHelpers>`|`[--noEmitHelpers ↗](https://www.typescriptlang.org/tsconfig.html#noEmitHelpers)`|
|Disable generating custom helper functions like `__extends` in compiled output.<br>|
|`<TypeScriptInlineSourceMap>`|`[--inlineSourceMap ↗](https://www.typescriptlang.org/tsconfig.html#inlineSourceMap)`|
|Include sourcemap files inside the emitted JavaScript.<br>|
|`<TypeScriptInlineSources>`|`[--inlineSources ↗](https://www.typescriptlang.org/tsconfig.html#inlineSources)`|
|Include source code in the sourcemaps inside the emitted JavaScript.<br>|
|`<TypeScriptNewLine>`|`[--newLine ↗](https://www.typescriptlang.org/tsconfig.html#newLine)`|
|Set the newline character for emitting files.<br>|
|`<TypeScriptIsolatedModules>`|`[--isolatedModules ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules)`|
|Ensure that each file can be safely transpiled without relying on other imports.<br>|
|`<TypeScriptEmitDecoratorMetadata>`|`[--emitDecoratorMetadata ↗](https://www.typescriptlang.org/tsconfig.html#emitDecoratorMetadata)`|
|Emit design-type metadata for decorated declarations in source files.<br>|
|`<TypeScriptRootDir>`|`[--rootDir ↗](https://www.typescriptlang.org/tsconfig.html#rootDir)`|
|Specify the root folder within your source files.<br>|
|`<TypeScriptExperimentalDecorators>`|`[--experimentalDecorators ↗](https://www.typescriptlang.org/tsconfig.html#experimentalDecorators)`|
|Enable experimental support for TC39 stage 2 draft decorators.<br>|
|`<TypeScriptModuleResolution>`|`[--moduleResolution ↗](https://www.typescriptlang.org/tsconfig.html#moduleResolution)`|
|Specify how TypeScript looks up a file from a given module specifier.<br>|
|`<TypeScriptSuppressExcessPropertyErrors>`|`[--suppressExcessPropertyErrors ↗](https://www.typescriptlang.org/tsconfig.html#suppressExcessPropertyErrors)`|
|Disable reporting of excess property errors during the creation of object literals.<br>|
|`<TypeScriptReactNamespace>`|`[--reactNamespace ↗](https://www.typescriptlang.org/tsconfig.html#reactNamespace)`|
|Specify the object invoked for `createElement`. This only applies when targeting `react` JSX emit.<br>|
|`<TypeScriptSkipDefaultLibCheck>`|`[--skipDefaultLibCheck ↗](https://www.typescriptlang.org/tsconfig.html#skipDefaultLibCheck)`|
|Skip type checking .d.ts files that are included with TypeScript.<br>|
|`<TypeScriptAllowUnusedLabels>`|`[--allowUnusedLabels ↗](https://www.typescriptlang.org/tsconfig.html#allowUnusedLabels)`|
|Disable error reporting for unused labels.<br>|
|`<TypeScriptNoImplicitReturns>`|`[--noImplicitReturns ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitReturns)`|
|Enable error reporting for codepaths that do not explicitly return in a function.<br>|
|`<TypeScriptNoFallthroughCasesInSwitch>`|`[--noFallthroughCasesInSwitch ↗](https://www.typescriptlang.org/tsconfig.html#noFallthroughCasesInSwitch)`|
|Enable error reporting for fallthrough cases in switch statements.<br>|
|`<TypeScriptAllowUnreachableCode>`|`[--allowUnreachableCode ↗](https://www.typescriptlang.org/tsconfig.html#allowUnreachableCode)`|
|Disable error reporting for unreachable code.<br>|
|`<TypeScriptForceConsistentCasingInFileNames>`|`[--forceConsistentCasingInFileNames ↗](https://www.typescriptlang.org/tsconfig.html#forceConsistentCasingInFileNames)`|
|Ensure that casing is correct in imports.<br>|
|`<TypeScriptAllowSyntheticDefaultImports>`|`[--allowSyntheticDefaultImports ↗](https://www.typescriptlang.org/tsconfig.html#allowSyntheticDefaultImports)`|
|Allow 'import x from y' when a module doesn't have a default export.<br>|
|`<TypeScriptNoImplicitUseStrict>`|`[--noImplicitUseStrict ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitUseStrict)`|
|Disable adding 'use strict' directives in emitted JavaScript files.<br>|
|`<TypeScriptLib>`|`[--lib ↗](https://www.typescriptlang.org/tsconfig.html#lib)`|
|Specify a set of bundled library declaration files that describe the target runtime environment.<br>|
|`<TypeScriptBaseUrl>`|`[--baseUrl ↗](https://www.typescriptlang.org/tsconfig.html#baseUrl)`|
|Specify the base directory to resolve non-relative module names.<br>|
|`<TypeScriptDeclarationDir>`|`[--declarationDir ↗](https://www.typescriptlang.org/tsconfig.html#declarationDir)`|
|Specify the output directory for generated declaration files.<br>|
|`<TypeScriptNoImplicitThis>`|`[--noImplicitThis ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitThis)`|
|Enable error reporting when `this` is given the type `any`.<br>|
|`<TypeScriptSkipLibCheck>`|`[--skipLibCheck ↗](https://www.typescriptlang.org/tsconfig.html#skipLibCheck)`|
|Skip type checking all .d.ts files.<br>|
|`<TypeScriptStrictNullChecks>`|`[--strictNullChecks ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks)`|
|When type checking, take into account `null` and `undefined`.<br>|
|`<TypeScriptNoUnusedLocals>`|`[--noUnusedLocals ↗](https://www.typescriptlang.org/tsconfig.html#noUnusedLocals)`|
|Enable error reporting when a local variables aren't read.<br>|
|`<TypeScriptNoUnusedParameters>`|`[--noUnusedParameters ↗](https://www.typescriptlang.org/tsconfig.html#noUnusedParameters)`|
|Raise an error when a function parameter isn't read<br>|
|`<TypeScriptAlwaysStrict>`|`[--alwaysStrict ↗](https://www.typescriptlang.org/tsconfig.html#alwaysStrict)`|
|Ensure 'use strict' is always emitted.<br>|
|`<TypeScriptImportHelpers>`|`[--importHelpers ↗](https://www.typescriptlang.org/tsconfig.html#importHelpers)`|
|Allow importing helper functions from tslib once per project, instead of including them per-file.<br>|
|`<TypeScriptJSXFactory>`|`[--jsxFactory ↗](https://www.typescriptlang.org/tsconfig.html#jsxFactory)`|
|Specify the JSX factory function used when targeting React JSX emit, e.g. 'React.createElement' or 'h'<br>|
|`<TypeScriptStripInternal>`|`[--stripInternal ↗](https://www.typescriptlang.org/tsconfig.html#stripInternal)`|
|Disable emitting declarations that have `@internal` in their JSDoc comments.<br>|
|`<TypeScriptCheckJs>`|`[--checkJs ↗](https://www.typescriptlang.org/tsconfig.html#checkJs)`|
|Enable error reporting in type-checked JavaScript files.<br>|
|`<TypeScriptDownlevelIteration>`|`[--downlevelIteration ↗](https://www.typescriptlang.org/tsconfig.html#downlevelIteration)`|
|Emit more compliant, but verbose and less performant JavaScript for iteration.<br>|
|`<TypeScriptStrict>`|`[--strict ↗](https://www.typescriptlang.org/tsconfig.html#strict)`|
|Enable all strict type checking options.<br>|
|`<TypeScriptNoStrictGenericChecks>`|`[--noStrictGenericChecks ↗](https://www.typescriptlang.org/tsconfig.html#noStrictGenericChecks)`|
|Disable strict checking of generic signatures in function types.<br>|
|`<TypeScriptPreserveSymlinks>`|`[--preserveSymlinks ↗](https://www.typescriptlang.org/tsconfig.html#preserveSymlinks)`|
|Disable resolving symlinks to their realpath. This correlates to the same flag in node.<br>|
|`<TypeScriptStrictFunctionTypes>`|`[--strictFunctionTypes ↗](https://www.typescriptlang.org/tsconfig.html#strictFunctionTypes)`|
|When assigning functions, check to ensure parameters and the return values are subtype-compatible.<br>|
|`<TypeScriptStrictPropertyInitialization>`|`[--strictPropertyInitialization ↗](https://www.typescriptlang.org/tsconfig.html#strictPropertyInitialization)`|
|Check for class properties that are declared but not set in the constructor.<br>|
|`<TypeScriptESModuleInterop>`|`[--esModuleInterop ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop)`|
|Emit additional JavaScript to ease support for importing CommonJS modules. This enables `allowSyntheticDefaultImports` for type compatibility.<br>|
|`<TypeScriptEmitDeclarationOnly>`|`[--emitDeclarationOnly ↗](https://www.typescriptlang.org/tsconfig.html#emitDeclarationOnly)`|
|Only output d.ts files and not JavaScript files.<br>|
|`<TypeScriptKeyofStringsOnly>`|`[--keyofStringsOnly ↗](https://www.typescriptlang.org/tsconfig.html#keyofStringsOnly)`|
|Make keyof only return strings instead of string, numbers or symbols. Legacy option.<br>|
|`<TypeScriptUseDefineForClassFields>`|`[--useDefineForClassFields ↗](https://www.typescriptlang.org/tsconfig.html#useDefineForClassFields)`|
|Emit ECMAScript-standard-compliant class fields.<br>|
|`<TypeScriptDeclarationMap>`|`[--declarationMap ↗](https://www.typescriptlang.org/tsconfig.html#declarationMap)`|
|Create sourcemaps for d.ts files.<br>|
|`<TypeScriptResolveJsonModule>`|`[--resolveJsonModule ↗](https://www.typescriptlang.org/tsconfig.html#resolveJsonModule)`|
|Enable importing .json files<br>|
|`<TypeScriptStrictBindCallApply>`|`[--strictBindCallApply ↗](https://www.typescriptlang.org/tsconfig.html#strictBindCallApply)`|
|Check that the arguments for `bind`, `call`, and `apply` methods match the original function.<br>|
|`<TypeScriptNoEmitOnError>`|`[--noEmitOnError ↗](https://www.typescriptlang.org/tsconfig.html#noEmitOnError)`|
|Disable emitting files if any type checking errors are reported.<br>|


### Additional Flags {#additional-flags}

Because the MSBuild system passes arguments directly to the TypeScript CLI, you can use the option `TypeScriptAdditionalFlags` to provide specific flags which don’t have a mapping above.

For example, this would turn on [`noPropertyAccessFromIndexSignature` ↗](https://www.typescriptlang.org/tsconfig.html#noPropertyAccessFromIndexSignature):

```xml
<TypeScriptAdditionalFlags> $(TypeScriptAdditionalFlags) --noPropertyAccessFromIndexSignature</TypeScriptAdditionalFlags>
```

### Debug and Release Builds {#debug-and-release-builds}

You can use PropertyGroup conditions to define different sets of configurations. For example, a common task is stripping comments and sourcemaps in production. In this example, we define a debug and release property group which have different TypeScript configurations:

```xml
<PropertyGroup Condition="'$(Configuration)' == 'Debug'">
<TypeScriptRemoveComments>false</TypeScriptRemoveComments>
<TypeScriptSourceMap>true</TypeScriptSourceMap>
</PropertyGroup>

<PropertyGroupCondition="'$(Configuration)' == 'Release'">
<TypeScriptRemoveComments>true</TypeScriptRemoveComments>
<TypeScriptSourceMap>false</TypeScriptSourceMap>
</PropertyGroup>

<Import
Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')"/>
```

### ToolsVersion {#toolsversion}

The value of `<TypeScriptToolsVersion>1.7</TypeScriptToolsVersion>` property in the project file identifies the compiler version to use to build (1.7 in this example).
This allows a project to build against the same versions of the compiler on different machines.

If `TypeScriptToolsVersion` is not specified, the latest compiler version installed on the machine will be used to build.

Users using newer versions of TS, will see a prompt to upgrade their project on first load.

### TypeScriptCompileBlocked {#typescriptcompileblocked}

If you are using a different build tool to build your project (e.g. gulp, grunt , etc.) and VS for the development and debugging experience, set `<TypeScriptCompileBlocked>true</TypeScriptCompileBlocked>` in your project.
This should give you all the editing support, but not the build when you hit F5.

### TypeScriptEnableIncrementalMSBuild (TypeScript 4.2 Beta and later) {#typescriptenableincrementalmsbuild-typescript-42-beta-and-later}

By default, MSBuild will attempt to only run the TypeScript compiler when the project’s source files have been updated since the last compilation.
However, if this behavior is causing issues, such as when TypeScript’s [`incremental` ↗](https://www.typescriptlang.org/tsconfig.html#incremental) option is enabled, set `<TypeScriptEnableIncrementalMSBuild>false</TypeScriptEnableIncrementalMSBuild>` to ensure the TypeScript compiler is invoked with every run of MSBuild.
