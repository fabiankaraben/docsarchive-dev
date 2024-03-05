---
linkTitle: "What is a tsconfig.json"
title: "TypeScript: Documentation - What is a tsconfig.json"
description: "Learn about how a TSConfig works"
weight: 1
type: docs
prev: /typescript/5.1/javascript/dts-from-js
canonical: /typescript/5.2/project-configuration/tsconfig-json
---

# What is a tsconfig.json

## Overview {#overview}

The presence of a `tsconfig.json` file in a directory indicates that the directory is the root of a TypeScript project.
The `tsconfig.json` file specifies the root files and the compiler options required to compile the project.

JavaScript projects can use a `jsconfig.json` file instead, which acts almost the same but has some JavaScript-related compiler flags enabled by default.

A project is compiled in one of the following ways:

## Using `tsconfig.json` or `jsconfig.json` {#using-tsconfigjson-or-jsconfigjson}

- By invoking tsc with no input files, in which case the compiler searches for the `tsconfig.json` file starting in the current directory and continuing up the parent directory chain.
- By invoking tsc with no input files and a `--project` (or just `-p`) command line option that specifies the path of a directory containing a `tsconfig.json` file, or a path to a valid `.json` file containing the configurations.

When input files are specified on the command line, `tsconfig.json` files are ignored.

## Examples {#examples}

Example `tsconfig.json` files:

- Using the [`files` ↗](https://www.typescriptlang.org/tsconfig.html#files) property

  ```
  {
  "[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
  "[module ↗](https://www.typescriptlang.org/tsconfig.html#module)": "commonjs",
  "[noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)": true,
  "[removeComments ↗](https://www.typescriptlang.org/tsconfig.html#removeComments)": true,
  "[preserveConstEnums ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums)": true,
  "[sourceMap ↗](https://www.typescriptlang.org/tsconfig.html#sourceMap)": true
    },
  "[files ↗](https://www.typescriptlang.org/tsconfig.html#files)": [
  "core.ts",
  "sys.ts",
  "types.ts",
  "scanner.ts",
  "parser.ts",
  "utilities.ts",
  "binder.ts",
  "checker.ts",
  "emitter.ts",
  "program.ts",
  "commandLineParser.ts",
  "tsc.ts",
  "diagnosticInformationMap.generated.ts"
    ]
  }
  ```

- Using the [`include` ↗](https://www.typescriptlang.org/tsconfig.html#include) and [`exclude` ↗](https://www.typescriptlang.org/tsconfig.html#exclude) properties

  ```
  {
  "[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
  "[module ↗](https://www.typescriptlang.org/tsconfig.html#module)": "system",
  "[noImplicitAny ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny)": true,
  "[removeComments ↗](https://www.typescriptlang.org/tsconfig.html#removeComments)": true,
  "[preserveConstEnums ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums)": true,
  "[outFile ↗](https://www.typescriptlang.org/tsconfig.html#outFile)": "../../built/local/tsc.js",
  "[sourceMap ↗](https://www.typescriptlang.org/tsconfig.html#sourceMap)": true
    },
  "[include ↗](https://www.typescriptlang.org/tsconfig.html#include)": ["src/**/*"],
  "[exclude ↗](https://www.typescriptlang.org/tsconfig.html#exclude)": ["**/*.spec.ts"]
  }
  ```

## TSConfig Bases {#tsconfig-bases}

Depending on the JavaScript runtime environment which you intend to run your code in, there may be a base configuration which you can use at [github.com/tsconfig/bases ↗](https://github.com/tsconfig/bases/).
These are `tsconfig.json` files which your project extends from which simplifies your `tsconfig.json` by handling the runtime support.

For example, if you were writing a project which uses Node.js version 12 and above, then you could use the npm module [`@tsconfig/node12` ↗](https://www.npmjs.com/package/@tsconfig/node12):

```
{
"[extends ↗](https://www.typescriptlang.org/tsconfig.html#extends)": "@tsconfig/node12/tsconfig.json",

"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[preserveConstEnums ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums)": true
  },

"[include ↗](https://www.typescriptlang.org/tsconfig.html#include)": ["src/**/*"],
"[exclude ↗](https://www.typescriptlang.org/tsconfig.html#exclude)": ["**/*.spec.ts"]
}
```

This lets your `tsconfig.json` focus on the unique choices for your project, and not all of the runtime mechanics. There are a few tsconfig bases already, and we’re hoping the community can add more for different environments.

## Details {#details}

The `"compilerOptions"` property can be omitted, in which case the compiler’s defaults are used. See our full list of supported [Compiler Options ↗](https://www.typescriptlang.org/tsconfig.html).

## TSConfig Reference {#tsconfig-reference}

To learn more about the hundreds of configuration options in the [TSConfig Reference ↗](https://www.typescriptlang.org/tsconfig.html).

## Schema {#schema}

The `tsconfig.json` Schema can be found at [the JSON Schema Store ↗](https://web.archive.org/web/20230907160727/http://json.schemastore.org/tsconfig).
