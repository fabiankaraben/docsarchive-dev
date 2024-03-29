---
linkTitle: "TypeScript 5.1"
title: "TypeScript: Documentation - TypeScript 5.1"
description: "TypeScript 5.1 Release Notes"
weight: 1
type: docs
prev: /typescript/5.1/tutorials/babel-with-typescript
canonical: /typescript/5.2/whats-new/typescript-5-1
---

# TypeScript 5.1

## Easier Implicit Returns for `undefined`-Returning Functions {#easier-implicit-returns-for-undefined-returning-functions}

In JavaScript, if a function finishes running without hitting a `return`, it returns the value `undefined`.

```ts
function foo() {
// no return
}
// x = undefined
letx = foo();
```

However, in previous versions of TypeScript, the *only* functions that could have absolutely no return statements were `void`- and `any`-returning functions.
That meant that even if you explicitly said “this function returns `undefined`” you were forced to have at least one return statement.

```ts
// ✅ fine - we inferred that 'f1' returns 'void'
functionf1() {
// no returns
}
// ✅ fine - 'void' doesn't need a return statement
functionf2(): void {
// no returns
}
// ✅ fine - 'any' doesn't need a return statement
functionf3(): any {
// no returns
}
// ❌ error!
// A function whose declared type is neither 'void' nor 'any' must return a value.
functionf4(): undefined {
// no returns
}
```

This could be a pain if some API expected a function returning `undefined` - you would need to have either at least one explicit return of `undefined` or a `return` statement *and* an explicit annotation.

```ts
declare function takesFunction(f: () => undefined): undefined;
// ❌ error!
// Argument of type '() => void' is not assignable to parameter of type '() => undefined'.
takesFunction(() => {
// no returns
});
// ❌ error!
// A function whose declared type is neither 'void' nor 'any' must return a value.
takesFunction((): undefined=> {
// no returns
});
// ❌ error!
// Argument of type '() => void' is not assignable to parameter of type '() => undefined'.
takesFunction(() => {
return;
});
// ✅ works
takesFunction(() => {
returnundefined;
});
// ✅ works
takesFunction((): undefined=> {
return;
});
```

This behavior was frustrating and confusing, especially when calling functions outside of one’s control.
Understanding the interplay between inferring `void` over `undefined`, whether an `undefined`-returning function needs a `return` statement, etc. seems like a distraction.

First, TypeScript 5.1 now allows `undefined`-returning functions to have no return statement.

```ts
// ✅ Works in TypeScript 5.1!
functionf4(): undefined {
// no returns
}
// ✅ Works in TypeScript 5.1!
takesFunction((): undefined=> {
// no returns
});
```

Second, if a function has no return expressions and is being passed to something expecting a function that returns `undefined`, TypeScript infers `undefined` for that function’s return type.

```ts
// ✅ Works in TypeScript 5.1!
takesFunction(functionf() {
//                 ^ return type is undefined
// no returns
});
// ✅ Works in TypeScript 5.1!
takesFunction(functionf() {
//                 ^ return type is undefined
return;
});
```

To address another similar pain-point, under TypeScript’s `--noImplicitReturns` option, functions returning *only*`undefined` now have a similar exception to `void`, in that not every single code path must end in an explicit `return`.

```ts
// ✅ Works in TypeScript 5.1 under '--noImplicitReturns'!
functionf(): undefined {
if (Math.random()) {
// do some stuff...
return;
    }
}
```

For more information, you can read up on [the original issue ↗](https://github.com/microsoft/TypeScript/issues/36288) and [the implementing pull request ↗](https://github.com/microsoft/TypeScript/pull/53607).

## Unrelated Types for Getters and Setters {#unrelated-types-for-getters-and-setters}

TypeScript 4.3 made it possible to say that a `get` and `set` accessor pair might specify two different types.

```ts
interface Serializer {
setvalue(v: string | number | boolean);
getvalue(): string;
}
declareletbox: Serializer;
// Allows writing a 'boolean'
box.value = true;
// Comes out as a 'string'
console.log(box.value.toUpperCase());
```

Initially we required that the `get` type had to be a subtype of the `set` type.
This meant that writing

```ts
box.value = box.value;
```

would always be valid.

However, there are plenty of existing and proposed APIs that have completely unrelated types between their getters and setters.
For example, consider one of the most common examples - the `style` property in the DOM and [`CSSStyleRule` ↗](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleRule) API.
Every style rule has [a `style` property ↗](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleRule/style) that is a [`CSSStyleDeclaration` ↗](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration);
however, if you try to write to that property, it will only work correctly with a string!

TypeScript 5.1 now allows completely unrelated types for `get` and `set` accessor properties, provided that they have explicit type annotations.
And while this version of TypeScript does not yet change the types for these built-in interfaces, `CSSStyleRule` can now be defined in the following way:

```ts
interface CSSStyleRule {
// ...
/** Always reads as a `CSSStyleDeclaration` */
getstyle(): CSSStyleDeclaration;
/** Can only write a `string` here. */
setstyle(newValue: string);
// ...
}
```

This also allows other patterns like requiring `set` accessors to accept only “valid” data, but specifying that `get` accessors may return `undefined` if some underlying state hasn’t been initialized yet.

```ts
class SafeBox {

#value: string | undefined;

// Only accepts strings!
setvalue(newValue: string) {
    }
// Must check for 'undefined'!
getvalue(): string | undefined {
returnthis.#value;
    }
}
```

In fact, this is similar to how optional properties are checked under `--exactOptionalProperties`.

You can read up more on [the implementing pull request ↗](https://github.com/microsoft/TypeScript/pull/53417).

## Decoupled Type-Checking Between JSX Elements and JSX Tag Types {#decoupled-type-checking-between-jsx-elements-and-jsx-tag-types}

One pain point TypeScript had with JSX was its requirements on the type of every JSX element’s tag.

For context, a JSX element is either of the following:

```tsx
// A self-closing JSX tag
<Foo/>
// A regular element with an opening/closing tag
<Bar></Bar>
```

When type-checking `<Foo />` or `<Bar></Bar>`, TypeScript always looks up a namespace called `JSX` and fetches a type out of it called `Element` - or more directly, it looks up `JSX.Element`.

But to check whether `Foo` or `Bar` themselves were valid to use as tag names, TypeScript would roughly just grab the types returned or constructed by `Foo` or `Bar` and check for compatibility with `JSX.Element` (or another type called `JSX.ElementClass` if the type is constructable).

The limitations here meant that components could not be used if they returned or “rendered” a more broad type than just `JSX.Element`.
For example, a JSX library might be fine with a component returning `string`s or `Promise`s.

As a more concrete example, [React is considering adding limited support for components that return `Promise`s ↗](https://github.com/acdlite/rfcs/blob/first-class-promises/text/0000-first-class-support-for-promises.md), but existing versions of TypeScript cannot express that without someone drastically loosening the type of `JSX.Element`.

```tsx
import * as React from "react";
asyncfunctionFoo() {
return<div></div>;
}
letelement = <Foo/>;
//             ~~~
// 'Foo' cannot be used as a JSX component.
//   Its return type 'Promise<Element>' is not a valid JSX element.
```

To provide libraries with a way to express this, TypeScript 5.1 now looks up a type called `JSX.ElementType`.
`ElementType` specifies precisely what is valid to use as a tag in a JSX element.
So it might be typed today as something like

```tsx
namespace JSX {
exporttypeElementType =
// All the valid lowercase tags
keyofIntrinsicAttributes
// Function components
        (props: any) =>Element
// Class components
new (props: any) =>ElementClass;
exportinterfaceIntrinsicAttributesextends/*...*/ {}
exporttypeElement = /*...*/;
exporttypeElementClass = /*...*/;
}
```

We’d like to extend our thanks to [Sebastian Silbermann ↗](https://github.com/eps1lon) who contributed [this change ↗](https://github.com/microsoft/TypeScript/pull/51328)!

## Namespaced JSX Attributes {#namespaced-jsx-attributes}

TypeScript now supports namespaced attribute names when using JSX.

```tsx
import * as React from "react";
// Both of these are equivalent:
constx = <Fooa:b="hello"/>;
consty = <Fooa:b="hello"/>;
interfaceFooProps {
"a:b": string;
}
functionFoo(props: FooProps) {
return<div>{props["a:b"]}</div>;
}
```

Namespaced tag names are looked up in a similar way on `JSX.IntrinsicAttributes` when the first segment of the name is a lowercase name.

```tsx
// In some library's code or in an augmentation of that library:
namespaceJSX {
interfaceIntrinsicElements {
        ["a:b"]: { prop: string };
    }
}
// In our code:
letx = <a:bprop="hello!"/>;
```

[This contribution ↗](https://github.com/microsoft/TypeScript/pull/53799) was provided thanks to [Oleksandr Tarasiuk ↗](https://github.com/a-tarasyuk).

## `typeRoots` Are Consulted In Module Resolution {#typeroots-are-consulted-in-module-resolution}

When TypeScript’s specified module lookup strategy is unable to resolve a path, it will now resolve packages relative to the specified `typeRoots`.

See [this pull request ↗](https://github.com/microsoft/TypeScript/pull/51715) for more details.

## Move Declarations to Existing Files {#move-declarations-to-existing-files}

In addition to moving declarations to new files, TypeScript now ships a preview feature for moving declarations to existing files as well.
You can try this functionality out in a recent version of Visual Studio Code.

![Moving a function 'getThanks' to an existing file in the workspace.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2023/05/moveToFile-5.1-preview.gif)

Keep in mind that this feature is currently in preview, and we are seeking further feedback on it.

[https://github.com/microsoft/TypeScript/pull/53542](https://github.com/microsoft/TypeScript/pull/53542)

## Linked Cursors for JSX Tags {#linked-cursors-for-jsx-tags}

TypeScript now supports *linked editing* for JSX tag names.
Linked editing (occasionally called “mirrored cursors”) allows an editor to edit multiple locations at the same time automatically.

![An example of JSX tags with linked editing modifying a JSX fragment and a div element.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2023/04/linkedEditingJsx-5.1-1.gif)

This new feature should work in both TypeScript and JavaScript files, and can be enabled in Visual Studio Code Insiders.
In Visual Studio Code, you can either edit the `Editor: Linked Editing` option in the Settings UI:

![Visual Studio Code's Editor: Linked Editing` option](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2023/04/linkedEditing-5.1-vscode-ui-1.png)

or configure `editor.linkedEditing` in your JSON settings file:

```jsonc
{
// ...
"editor.linkedEditing": true,
}
```

This feature will also be supported by Visual Studio 17.7 Preview 1.

You can take a look at [our implementation of linked editing](https://github.com/microsoft/TypeScript/pull/53284) here!

## Snippet Completions for `@param` JSDoc Tags {#snippet-completions-for-param-jsdoc-tags}

TypeScript now provides snippet completions when typing out a `@param` tag in both TypeScript and JavaScript files.
This can help cut down on some typing and jumping around text as you document your code or add JSDoc types in JavaScript.

![An example of completing JSDoc param comments on an 'add' function.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2023/04/paramTagSnippets-5-1-1.gif)

You can [check out how this new feature was implemented on GitHub](https://github.com/microsoft/TypeScript/pull/53260).

## Optimizations {#optimizations}

### Avoiding Unnecessary Type Instantiation {#avoiding-unnecessary-type-instantiation}

TypeScript 5.1 now avoids performing type instantiation within object types that are known not to contain references to outer type parameters.
This has the potential to cut down on many unnecessary computations, and reduced the type-checking time of [material-ui’s docs directory](https://github.com/mui/material-ui/tree/b0351248fb396001a30330daac86d0e0794a0c1d/docs) by over 50%.

You can [see the changes involved for this change on GitHub](https://github.com/microsoft/TypeScript/pull/53246).

### Negative Case Checks for Union Literals {#negative-case-checks-for-union-literals}

When checking if a source type is part of a union type, TypeScript will first do a fast look-up using an internal type identifier for that source.
If that look-up fails, then TypeScript checks for compatibility against every type within the union.

When relating a literal type to a union of purely literal types, TypeScript can now avoid that full walk against every other type in the union.
This assumption is safe because TypeScript always interns/caches literal types - though there are some edge cases to handle relating to “fresh” literal types.

[This optimization](https://github.com/microsoft/TypeScript/pull/53192) was able to reduce the type-checking time of [the code in this issue](https://github.com/microsoft/TypeScript/issues/53191) from about 45 seconds to about 0.4 seconds.

### Reduced Calls into Scanner for JSDoc Parsing {#reduced-calls-into-scanner-for-jsdoc-parsing}

When older versions of TypeScript parsed out a JSDoc comment, they would use the scanner/tokenizer to break the comment into fine-grained tokens and piece the contents back together.
This could be helpful for normalizing comment text, so that multiple spaces would just collapse into one;
but it was extremely “chatty” and meant the parser and scanner would jump back and forth very often, adding overhead to JSDoc parsing.

TypeScript 5.1 has moved more logic around breaking down JSDoc comments into the scanner/tokenizer.
The scanner now returns larger chunks of content directly to the parser to do as it needs.

[These changes](https://github.com/microsoft/TypeScript/pull/53081) have brought down the parse time of several 10Mb mostly-prose-comment JavaScript files by about half.
For a more realistic example, our performance suite’s snapshot of [xstate](https://github.com/statelyai/xstate) dropped about 300ms of parse time, making it faster to load and analyze.

## Breaking Changes {#breaking-changes}

### ES2020 and Node.js 14.17 as Minimum Runtime Requirements {#es2020-and-nodejs-1417-as-minimum-runtime-requirements}

TypeScript 5.1 now ships JavaScript functionality that was introduced in ECMAScript 2020.
As a result, at minimum TypeScript must be run in a reasonably modern runtime.
For most users, this means TypeScript now only runs on Node.js 14.17 and later.

If you try running TypeScript 5.1 under an older version of Node.js such as Node 10 or 12, you may see an error like the following from running either `tsc.js` or `tsserver.js`:

```
node_modules/typescript/lib/tsserver.js:2406
  for (let i = startIndex ?? 0; i < array.length; i++) {
                           ^
 
SyntaxError: Unexpected token '?'
    at wrapSafe (internal/modules/cjs/loader.js:915:16)
    at Module._compile (internal/modules/cjs/loader.js:963:27)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1027:10)
    at Module.load (internal/modules/cjs/loader.js:863:32)
    at Function.Module._load (internal/modules/cjs/loader.js:708:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:60:12)
    at internal/main/run_main_module.js:17:47
```

Additionally, if you try installing TypeScript you’ll get something like the following error messages from npm:

```
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'typescript@5.1.1-rc',
npm WARN EBADENGINE   required: { node: '>=14.17' },
npm WARN EBADENGINE   current: { node: 'v12.22.12', npm: '8.19.2' }
npm WARN EBADENGINE }
```

from Yarn:

```
error typescript@5.1.1-rc: The engine "node" is incompatible with this module. Expected version ">=14.17". Got "12.22.12"
error Found incompatible module.
```

[See more information around this change here](https://github.com/microsoft/TypeScript/pull/53291).

### Explicit `typeRoots` Disables Upward Walks for `node_modules/@types` {#explicit-typeroots-disables-upward-walks-for-node_modulestypes}

Previously, when the `typeRoots` option was specified in a `tsconfig.json` but resolution to any `typeRoots` directories had failed, TypeScript would still continue walking up parent directories, trying to resolve packages within each parent’s `node_modules/@types` folder.

This behavior could prompt excessive look-ups and has been disabled in TypeScript 5.1.
As a result, you may begin to see errors like the following based on entries in your `tsconfig.json`’s `types` option or `/// <reference >` directives

```
error TS2688: Cannot find type definition file for 'node'.
error TS2688: Cannot find type definition file for 'mocha'.
error TS2688: Cannot find type definition file for 'jasmine'.
error TS2688: Cannot find type definition file for 'chai-http'.
error TS2688: Cannot find type definition file for 'webpack-env"'.
```

The solution is typically to add specific entries for `node_modules/@types` to your `typeRoots`:

```jsonc
{
"compilerOptions": {
"types": [
"node",
"mocha"
        ],
"typeRoots": [
// Keep whatever you had around before.
"./some-custom-types/",
// You might need your local 'node_modules/@types'.
"./node_modules/@types",
// You might also need to specify a shared 'node_modules/@types'
// if you're using a "monorepo" layout.
"../../node_modules/@types",
        ]
    }
}
```

More information is available [on the original change on our issue tracker](https://github.com/microsoft/TypeScript/pull/51715).
