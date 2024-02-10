---
linkTitle: "Integrating with Build Tools"
title: "TypeScript: Documentation - Integrating with Build Tools"
description: "How to use TypeScript with other build tools"
weight: 6
type: docs
---

# Integrating with Build Tools

## Babel {#babel}

### Install {#install}

```sh
npm install @babel/cli @babel/core @babel/preset-typescript --save-dev
```

### .babelrc {#babelrc}

```js
{
  "presets": ["@babel/preset-typescript"]
}
```

### Using Command Line Interface {#using-command-line-interface}

```sh
./node_modules/.bin/babel --out-file bundle.js src/index.ts
```

### package.json {#packagejson}

```js
{
  "scripts": {
    "build": "babel --out-file bundle.js main.ts"
  },
}
```

### Execute Babel from the command line {#execute-babel-from-the-command-line}

```sh
npm run build
```

## Browserify {#browserify}

### Install {#install-1}

```sh
npm install tsify
```

### Using Command Line Interface {#using-command-line-interface-1}

```sh
browserify main.ts -p [ tsify --noImplicitAny ] > bundle.js
```

### Using API {#using-api}

```js
var browserify = require("browserify");
var tsify = require("tsify");

browserify()
  .add("main.ts")
  .plugin("tsify", { noImplicitAny: true })
  .bundle()
  .pipe(process.stdout);
```

More details: [smrq/tsify](https://github.com/smrq/tsify)

## Grunt {#grunt}

### Using `grunt-ts` (no longer maintained) {#using-grunt-ts-no-longer-maintained}

#### Install {#install-2}

```sh
npm install grunt-ts --save-dev
```

#### Basic Gruntfile.js {#basic-gruntfilejs}

```js
module.exports = function (grunt) {
  grunt.initConfig({
    ts: {
      default: {
        src: ["**/*.ts", "!node_modules/**/*.ts"],
      },
    },
  });
  grunt.loadNpmTasks("grunt-ts");
  grunt.registerTask("default", ["ts"]);
};
```

More details: [TypeStrong/grunt-ts](https://github.com/TypeStrong/grunt-ts)

### Using `grunt-browserify` combined with `tsify` {#using-grunt-browserify-combined-with-tsify}

#### Install {#install-3}

```sh
npm install grunt-browserify tsify --save-dev
```

#### Basic Gruntfile.js {#basic-gruntfilejs-1}

```js
module.exports = function (grunt) {
  grunt.initConfig({
    browserify: {
      all: {
        src: "src/main.ts",
        dest: "dist/main.js",
        options: {
          plugin: ["tsify"],
        },
      },
    },
  });
  grunt.loadNpmTasks("grunt-browserify");
  grunt.registerTask("default", ["browserify"]);
};
```

More details: [jmreidy/grunt-browserify](https://github.com/jmreidy/grunt-browserify), [TypeStrong/tsify](https://github.com/TypeStrong/tsify)

## Gulp {#gulp}

### Install {#install-4}

```sh
npm install gulp-typescript
```

### Basic gulpfile.js {#basic-gulpfilejs}

```js
var gulp = require("gulp");
var ts = require("gulp-typescript");

gulp.task("default", function () {
  var tsResult = gulp.src("src/*.ts").pipe(
    ts({
      noImplicitAny: true,
      out: "output.js",
    })
  );
  return tsResult.js.pipe(gulp.dest("built/local"));
});
```

More details: [ivogabe/gulp-typescript](https://github.com/ivogabe/gulp-typescript)

## Jspm {#jspm}

### Install {#install-5}

```sh
npm install -g jspm@beta
```

*Note: Currently TypeScript support in jspm is in 0.16beta*

More details: [TypeScriptSamples/jspm](https://github.com/Microsoft/TypeScriptSamples/tree/master/jspm)

## MSBuild {#msbuild}

Update project file to include locally installed `Microsoft.TypeScript.Default.props` (at the top) and `Microsoft.TypeScript.targets` (at the bottom) files:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <!-- Include default props at the top -->
  <Import
      Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props"
      Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props')" />

  <!-- TypeScript configurations go here -->
  <PropertyGroup Condition="'$(Configuration)' == 'Debug'">
    <TypeScriptRemoveComments>false</TypeScriptRemoveComments>
    <TypeScriptSourceMap>true</TypeScriptSourceMap>
  </PropertyGroup>
  <PropertyGroup Condition="'$(Configuration)' == 'Release'">
    <TypeScriptRemoveComments>true</TypeScriptRemoveComments>
    <TypeScriptSourceMap>false</TypeScriptSourceMap>
  </PropertyGroup>

  <!-- Include default targets at the bottom -->
  <Import
      Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
      Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />
</Project>
```

More details about defining MSBuild compiler options: [Setting Compiler Options in MSBuild projects](/typescript/5.2/project-configuration/compiler-options-in-msbuild)

## NuGet {#nuget}

- Right-Click -> Manage NuGet Packages
- Search for `Microsoft.TypeScript.MSBuild`
- Hit `Install`
- When install is complete, rebuild!

More details can be found at [Package Manager Dialog](http://docs.nuget.org/Consume/Package-Manager-Dialog) and [using nightly builds with NuGet](https://github.com/Microsoft/TypeScript/wiki/Nightly-drops#using-nuget-with-msbuild)

## Rollup {#rollup}

### Install {#install-6}

```
npm install @rollup/plugin-typescript --save-dev
```

Note that both `typescript` and `tslib` are peer dependencies of this plugin that need to be installed separately.

### Usage {#usage}

Create a `rollup.config.js`[configuration file](https://www.rollupjs.org/guide/en/#configuration-files) and import the plugin:

```js
// rollup.config.js
import typescript from '@rollup/plugin-typescript';

export default {
  input: 'src/index.ts',
  output: {
    dir: 'output',
    format: 'cjs'
  },
  plugins: [typescript()]
};
```

## Svelte Compiler {#svelte-compiler}

### Install {#install-7}

```
npm install --save-dev svelte-preprocess
```

Note that `typescript` is an optional peer dependencies of this plugin and needs to be installed separately. `tslib` is not provided either.

You may also consider [`svelte-check`](https://www.npmjs.com/package/svelte-check) for CLI type checking.

### Usage {#usage-1}

Create a `svelte.config.js` configuration file and import the plugin:

```js
// svelte.config.js
import preprocess from 'svelte-preprocess';

const config = {
  // Consult https://github.com/sveltejs/svelte-preprocess
  // for more information about preprocessors
  preprocess: preprocess()
};

export default config;
```

You can now specify that script blocks are written in TypeScript:

```
<script lang="ts">
```

## Vite {#vite}

Vite supports importing `.ts` files out-of-the-box. It only performs transpilation and not type checking. It also requires that some `compilerOptions` have certain values. See the [Vite docs](https://vitejs.dev/guide/features.html#typescript) for more details.

## Webpack {#webpack}

### Install {#install-8}

```sh
npm install ts-loader --save-dev
```

### Basic webpack.config.js when using Webpack 5 or 4 {#basic-webpackconfigjs-when-using-webpack-5-or-4}

```js
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

See [more details on ts-loader here](https://www.npmjs.com/package/ts-loader).

Alternatives:

- [awesome-typescript-loader](https://www.npmjs.com/package/awesome-typescript-loader)
