---
linkTitle: "Migrating from JavaScript"
title: "TypeScript: Documentation - Migrating from JavaScript"
description: "How to migrate from JavaScript to TypeScript"
weight: 4
type: docs
---

# Migrating from JavaScript

TypeScript doesn’t exist in a vacuum.
It was built with the JavaScript ecosystem in mind, and a lot of JavaScript exists today.
Converting a JavaScript codebase over to TypeScript is, while somewhat tedious, usually not challenging.
In this tutorial, we’re going to look at how you might start out.
We assume you’ve read enough of the handbook to write new TypeScript code.

If you’re looking to convert a React project, we recommend looking at the [React Conversion Guide ↗](https://github.com/Microsoft/TypeScript-React-Conversion-Guide#typescript-react-conversion-guide) first.

## Setting up your Directories {#setting-up-your-directories}

If you’re writing in plain JavaScript, it’s likely that you’re running your JavaScript directly,
where your `.js` files are in a `src`, `lib`, or `dist` directory, and then run as desired.

If that’s the case, the files that you’ve written are going to be used as inputs to TypeScript, and you’ll run the outputs it produces.
During our JS to TS migration, we’ll need to separate our input files to prevent TypeScript from overwriting them.
If your output files need to reside in a specific directory, then that will be your output directory.

You might also be running some intermediate steps on your JavaScript, such as bundling or using another transpiler like Babel.
In this case, you might already have a folder structure like this set up.

From this point on, we’re going to assume that your directory is set up something like this:

```
projectRoot
├── src
│   ├── file1.js
│   └── file2.js
├── built
└── tsconfig.json
```

If you have a `tests` folder outside of your `src` directory, you might have one `tsconfig.json` in `src`, and one in `tests` as well.

## Writing a Configuration File {#writing-a-configuration-file}

TypeScript uses a file called `tsconfig.json` for managing your project’s options, such as which files you want to include, and what sorts of checking you want to perform.
Let’s create a bare-bones one for our project:

```json
{
"compilerOptions": {
"outDir": "./built",
"allowJs": true,
"target": "es5"
  },
"include": ["./src/**/*"]
}
```

Here we’re specifying a few things to TypeScript:

1. Read in any files it understands in the `src` directory (with [`include` ↗](https://www.typescriptlang.org/tsconfig.html#include)).
2. Accept JavaScript files as inputs (with [`allowJs` ↗](https://www.typescriptlang.org/tsconfig.html#allowJs)).
3. Emit all of the output files in `built` (with [`outDir` ↗](https://www.typescriptlang.org/tsconfig.html#outDir)).
4. Translate newer JavaScript constructs down to an older version like ECMAScript 5 (using [`target` ↗](https://www.typescriptlang.org/tsconfig.html#target)).

At this point, if you try running `tsc` at the root of your project, you should see output files in the `built` directory.
The layout of files in `built` should look identical to the layout of `src`.
You should now have TypeScript working with your project.

## Early Benefits {#early-benefits}

Even at this point you can get some great benefits from TypeScript understanding your project.
If you open up an editor like [VS Code ↗](https://code.visualstudio.com/) or [Visual Studio ↗](https://visualstudio.com/), you’ll see that you can often get some tooling support like completion.
You can also catch certain bugs with options like:

- [`noImplicitReturns` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitReturns) which prevents you from forgetting to return at the end of a function.
- [`noFallthroughCasesInSwitch` ↗](https://www.typescriptlang.org/tsconfig.html#noFallthroughCasesInSwitch) which is helpful if you never want to forget a `break` statement between `case`s in a `switch` block.

TypeScript will also warn about unreachable code and labels, which you can disable with [`allowUnreachableCode` ↗](https://www.typescriptlang.org/tsconfig.html#allowUnreachableCode) and [`allowUnusedLabels` ↗](https://www.typescriptlang.org/tsconfig.html#allowUnusedLabels) respectively.

## Integrating with Build Tools {#integrating-with-build-tools}

You might have some more build steps in your pipeline.
Perhaps you concatenate something to each of your files.
Each build tool is different, but we’ll do our best to cover the gist of things.

### Gulp {#gulp}

If you’re using Gulp in some fashion, we have a tutorial on [using Gulp](/typescript/5.1/tutorials/gulp) with TypeScript, and integrating with common build tools like Browserify, Babelify, and Uglify.
You can read more there.

### Webpack {#webpack}

Webpack integration is pretty simple.
You can use `ts-loader`, a TypeScript loader, combined with `source-map-loader` for easier debugging.
Simply run

```shell
npm install ts-loader source-map-loader
```

and merge in options from the following into your `webpack.config.js` file:

```js
module.exports = {
entry:"./src/index.ts",
output: {
filename:"./dist/bundle.js",
  },

// Enable sourcemaps for debugging webpack's output.
devtool:"source-map",

resolve: {
// Add '.ts' and '.tsx' as resolvable extensions.
extensions: ["", ".webpack.js", ".web.js", ".ts", ".tsx", ".js"],
  },

module: {
rules: [
// All files with a '.ts' or '.tsx' extension will be handled by 'ts-loader'.
      { test: /\.tsx?$/, loader:"ts-loader" },

// All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
      { test: /\.js$/, loader:"source-map-loader" },
    ],
  },

// Other options...
};
```

It’s important to note that ts-loader will need to run before any other loader that deals with `.js` files.

You can see an example of using Webpack in our [tutorial on React and Webpack ↗](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html).

## Moving to TypeScript Files {#moving-to-typescript-files}

At this point, you’re probably ready to start using TypeScript files.
The first step is to rename one of your `.js` files to `.ts`.
If your file uses JSX, you’ll need to rename it to `.tsx`.

Finished with that step?
Great!
You’ve successfully migrated a file from JavaScript to TypeScript!

Of course, that might not feel right.
If you open that file in an editor with TypeScript support (or if you run `tsc --pretty`), you might see red squiggles on certain lines.
You should think of these the same way you’d think of red squiggles in an editor like Microsoft Word.
TypeScript will still translate your code, just like Word will still let you print your documents.

If that sounds too lax for you, you can tighten that behavior up.
If, for instance, you *don’t* want TypeScript to compile to JavaScript in the face of errors, you can use the [`noEmitOnError` ↗](https://www.typescriptlang.org/tsconfig.html#noEmitOnError) option.
In that sense, TypeScript has a dial on its strictness, and you can turn that knob up as high as you want.

If you plan on using the stricter settings that are available, it’s best to turn them on now (see [Getting Stricter Checks](/typescript/5.1/tutorials/migrating-from-javascript#getting-stricter-checks) below).
For instance, if you never want TypeScript to silently infer `any` for a type without you explicitly saying so, you can use [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny) before you start modifying your files.
While it might feel somewhat overwhelming, the long-term gains become apparent much more quickly.

### Weeding out Errors {#weeding-out-errors}

Like we mentioned, it’s not unexpected to get error messages after conversion.
The important thing is to actually go one by one through these and decide how to deal with the errors.
Often these will be legitimate bugs, but sometimes you’ll have to explain what you’re trying to do a little better to TypeScript.

#### Importing from Modules {#importing-from-modules}

You might start out getting a bunch of errors like `Cannot find name 'require'.`, and `Cannot find name 'define'.`.
In these cases, it’s likely that you’re using modules.
While you can just convince TypeScript that these exist by writing out

```ts
// For Node/CommonJS
declarefunctionrequire(path: string): any;
```

or

```ts
// For RequireJS/AMD
declarefunctiondefine(...args: any[]): any;
```

it’s better to get rid of those calls and use TypeScript syntax for imports.

First, you’ll need to enable some module system by setting TypeScript’s [`module` ↗](https://www.typescriptlang.org/tsconfig.html#module) option.
Valid options are `commonjs`, `amd`, `system`, and `umd`.

If you had the following Node/CommonJS code:

```js
var foo = require("foo");

foo.doStuff();
```

or the following RequireJS/AMD code:

```js
define(["foo"], function (foo) {
foo.doStuff();
});
```

then you would write the following TypeScript code:

```ts
import foo = require("foo");

foo.doStuff();
```

#### Getting Declaration Files {#getting-declaration-files}

If you started converting over to TypeScript imports, you’ll probably run into errors like `Cannot find module 'foo'.`.
The issue here is that you likely don’t have *declaration files* to describe your library.
Luckily this is pretty easy.
If TypeScript complains about a package like `lodash`, you can just write

```shell
npm install -S @types/lodash
```

If you’re using a module option other than `commonjs`, you’ll need to set your [`moduleResolution` ↗](https://www.typescriptlang.org/tsconfig.html#moduleResolution) option to `node`.

After that, you’ll be able to import lodash with no issues, and get accurate completions.

#### Exporting from Modules {#exporting-from-modules}

Typically, exporting from a module involves adding properties to a value like `exports` or `module.exports`.
TypeScript allows you to use top-level export statements.
For instance, if you exported a function like so:

```js
module.exports.feedPets = function (pets) {
// ...
};
```

you could write that out as the following:

```ts
export function feedPets(pets) {
// ...
}
```

Sometimes you’ll entirely overwrite the exports object.
This is a common pattern people use to make their modules immediately callable like in this snippet:

```js
var express = require("express");
varapp = express();
```

You might have previously written that like so:

```js
function foo() {
// ...
}
module.exports = foo;
```

In TypeScript, you can model this with the `export =` construct.

```ts
function foo() {
// ...
}
export = foo;
```

#### Too many/too few arguments {#too-manytoo-few-arguments}

You’ll sometimes find yourself calling a function with too many/few arguments.
Typically, this is a bug, but in some cases, you might have declared a function that uses the `arguments` object instead of writing out any parameters:

```js
function myCoolFunction() {
if (arguments.length == 2 && !Array.isArray(arguments[1])) {
varf = arguments[0];
vararr = arguments[1];
// ...
  }
// ...
}

myCoolFunction(
function (x) {
console.log(x);
  },
  [1, 2, 3, 4]
);
myCoolFunction(
function (x) {
console.log(x);
  },
1,
2,
3,
4
);
```

In this case, we need to use TypeScript to tell any of our callers about the ways `myCoolFunction` can be called using function overloads.

```ts
function myCoolFunction(f: (x: number) => void, nums: number[]): void;
functionmyCoolFunction(f: (x: number) =>void, ...nums: number[]): void;
functionmyCoolFunction() {
if (arguments.length == 2 && !Array.isArray(arguments[1])) {
varf = arguments[0];
vararr = arguments[1];
// ...
  }
// ...
}
```

We added two overload signatures to `myCoolFunction`.
The first checks states that `myCoolFunction` takes a function (which takes a `number`), and then a list of `number`s.
The second one says that it will take a function as well, and then uses a rest parameter (`...nums`) to state that any number of arguments after that need to be `number`s.

#### Sequentially Added Properties {#sequentially-added-properties}

Some people find it more aesthetically pleasing to create an object and add properties immediately after like so:

```js
var options = {};
options.color = "red";
options.volume = 11;
```

TypeScript will say that you can’t assign to `color` and `volume` because it first figured out the type of `options` as `{}` which doesn’t have any properties.
If you instead moved the declarations into the object literal themselves, you’d get no errors:

```ts
let options = {
color:"red",
volume:11,
};
```

You could also define the type of `options` and add a type assertion on the object literal.

```ts
interface Options {
color: string;
volume: number;
}

letoptions = {} asOptions;
options.color = "red";
options.volume = 11;
```

Alternatively, you can just say `options` has the type `any` which is the easiest thing to do, but which will benefit you the least.

#### `any`, `Object`, and `{}` {#any-object-and-}

You might be tempted to use `Object` or `{}` to say that a value can have any property on it because `Object` is, for most purposes, the most general type.
However **`any` is actually the type you want to use** in those situations, since it’s the most *flexible* type.

For instance, if you have something that’s typed as `Object` you won’t be able to call methods like `toLowerCase()` on it.
Being more general usually means you can do less with a type, but `any` is special in that it is the most general type while still allowing you to do anything with it.
That means you can call it, construct it, access properties on it, etc.
Keep in mind though, whenever you use `any`, you lose out on most of the error checking and editor support that TypeScript gives you.

If a decision ever comes down to `Object` and `{}`, you should prefer `{}`.
While they are mostly the same, technically `{}` is a more general type than `Object` in certain esoteric cases.

### Getting Stricter Checks {#getting-stricter-checks}

TypeScript comes with certain checks to give you more safety and analysis of your program.
Once you’ve converted your codebase to TypeScript, you can start enabling these checks for greater safety.

#### No Implicit `any` {#no-implicit-any}

There are certain cases where TypeScript can’t figure out what certain types should be.
To be as lenient as possible, it will decide to use the type `any` in its place.
While this is great for migration, using `any` means that you’re not getting any type safety, and you won’t get the same tooling support you’d get elsewhere.
You can tell TypeScript to flag these locations down and give an error with the [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny) option.

#### Strict `null` & `undefined` Checks {#strict-null--undefined-checks}

By default, TypeScript assumes that `null` and `undefined` are in the domain of every type.
That means anything declared with the type `number` could be `null` or `undefined`.
Since `null` and `undefined` are such a frequent source of bugs in JavaScript and TypeScript, TypeScript has the [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) option to spare you the stress of worrying about these issues.

When [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) is enabled, `null` and `undefined` get their own types called `null` and `undefined` respectively.
Whenever anything is *possibly*`null`, you can use a union type with the original type.
So for instance, if something could be a `number` or `null`, you’d write the type out as `number | null`.

If you ever have a value that TypeScript thinks is possibly `null`/`undefined`, but you know better, you can use the postfix `!` operator to tell it otherwise.

```ts
declare var foo: string[] | null;

foo.length; // error - 'foo' is possibly 'null'

foo!.length; // okay - 'foo!' just has type 'string[]'
```

As a heads up, when using [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks), your dependencies may need to be updated to use [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) as well.

#### No Implicit `any` for `this` {#no-implicit-any-for-this}

When you use the `this` keyword outside of classes, it has the type `any` by default.
For instance, imagine a `Point` class, and imagine a function that we wish to add as a method:

```ts
class Point {
constructor(publicx, publicy) {}
getDistance(p: Point) {
letdx = p.x - this.x;
letdy = p.y - this.y;
returnMath.sqrt(dx ** 2 + dy ** 2);
  }
}
// ...

// Reopen the interface.
interfacePoint {
distanceFromOrigin(): number;
}
Point.prototype.distanceFromOrigin = function () {
returnthis.getDistance({ x:0, y:0 });
};
```

This has the same problems we mentioned above - we could easily have misspelled `getDistance` and not gotten an error.
For this reason, TypeScript has the [`noImplicitThis` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitThis) option.
When that option is set, TypeScript will issue an error when `this` is used without an explicit (or inferred) type.
The fix is to use a `this`-parameter to give an explicit type in the interface or in the function itself:

```ts
Point.prototype.distanceFromOrigin = function (this: Point) {
returnthis.getDistance({ x:0, y:0 });
};
```
