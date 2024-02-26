---
linkTitle: "TypeScript 4.9"
title: "TypeScript: Documentation - TypeScript 4.9"
description: "TypeScript 4.9 Release Notes"
weight: 3
type: docs
---

# TypeScript 4.9

## The `satisfies` Operator {#the-satisfies-operator}

TypeScript developers are often faced with a dilemma: we want to ensure that some expression *matches* some type, but also want to keep the *most specific* type of that expression for inference purposes.

For example:

```ts
// Each property can be a string or an RGB tuple.
constpalette = {
red: [255, 0, 0],
green:"#00ff00",
bleu: [0, 0, 255]
//  ^^^^ sacrebleu - we've made a typo!
};

// We want to be able to use array methods on 'red'...
constredComponent = palette.red.at(0);

// or string methods on 'green'...
constgreenNormalized = palette.green.toUpperCase();
```

Notice that we’ve written `bleu`, whereas we probably should have written `blue`.
We could try to catch that `bleu` typo by using a type annotation on `palette`, but we’d lose the information about each property.

```ts
type Colors = "red" | "green" | "blue";

typeRGB = [red: number, green: number, blue: number];

constpalette: Record<Colors, string | RGB> = {
red: [255, 0, 0],
green:"#00ff00",
bleu: [0, 0, 255]
//  ~~~~ The typo is now correctly detected
};

// But we now have an undesirable error here - 'palette.red' "could" be a string.
constredComponent = palette.red.at(0);
```

The new `satisfies` operator lets us validate that the type of an expression matches some type, without changing the resulting type of that expression.
As an example, we could use `satisfies` to validate that all the properties of `palette` are compatible with `string | number[]`:

```ts
type Colors = "red" | "green" | "blue";

typeRGB = [red: number, green: number, blue: number];

constpalette = {
red: [255, 0, 0],
green:"#00ff00",
bleu: [0, 0, 255]
//  ~~~~ The typo is now caught!
} satisfiesRecord<Colors, string | RGB>;

// Both of these methods are still accessible!
constredComponent = palette.red.at(0);
constgreenNormalized = palette.green.toUpperCase();
```

`satisfies` can be used to catch lots of possible errors.
For example, we could ensure that an object has *all* the keys of some type, but no more:

```ts
type Colors = "red" | "green" | "blue";

// Ensure that we have exactly the keys from 'Colors'.
constfavoriteColors = {
"red":"yes",
"green":false,
"blue":"kinda",
"platypus":false
//  ~~~~~~~~~~ error - "platypus" was never listed in 'Colors'.
} satisfiesRecord<Colors, unknown>;

// All the information about the 'red', 'green', and 'blue' properties are retained.
constg: boolean = favoriteColors.green;
```

Maybe we don’t care about if the property names match up somehow, but we do care about the types of each property.
In that case, we can also ensure that all of an object’s property values conform to some type.

```ts
type RGB = [red: number, green: number, blue: number];

constpalette = {
red: [255, 0, 0],
green:"#00ff00",
blue: [0, 0]
//    ~~~~~~ error!
} satisfiesRecord<string, string | RGB>;

// Information about each property is still maintained.
constredComponent = palette.red.at(0);
constgreenNormalized = palette.green.toUpperCase();
```

For more examples, you can see the [issue proposing this ↗](https://github.com/microsoft/TypeScript/issues/47920) and [the implementing pull request ↗](https://github.com/microsoft/TypeScript/pull/46827).
We’d like to thank [Oleksandr Tarasiuk ↗](https://github.com/a-tarasyuk) who implemented and iterated on this feature with us.

## Unlisted Property Narrowing with the `in` Operator {#unlisted-property-narrowing-with-the-in-operator}

As developers, we often need to deal with values that aren’t fully known at runtime.
In fact, we often don’t know if properties exist, whether we’re getting a response from a server or reading a configuration file.
JavaScript’s `in` operator can check whether a property
exists on an object.

Previously, TypeScript allowed us to narrow away any types that don’t explicitly list a property.

```ts
interface RGB {
red: number;
green: number;
blue: number;
}

interfaceHSV {
hue: number;
saturation: number;
value: number;
}

functionsetColor(color: RGB | HSV) {
if ("hue"incolor) {
// 'color' now has the type HSV
    }
// ...
}
```

Here, the type `RGB` didn’t list the `hue` and got narrowed away, and leaving us with the type `HSV`.

But what about examples where no type listed a given property?
In those cases, the language didn’t help us much.
Let’s take the following example in JavaScript:

```js
function tryGetPackageName(context) {
constpackageJSON = context.packageJSON;
// Check to see if we have an object.
if (packageJSON && typeofpackageJSON === "object") {
// Check to see if it has a string name property.
if ("name"inpackageJSON && typeofpackageJSON.name === "string") {
returnpackageJSON.name;
        }
    }

returnundefined;
}
```

Rewriting this to canonical TypeScript would just be a matter of defining and using a type for `context`;
however, picking a safe type like `unknown` for the `packageJSON` property would cause issues in older versions of TypeScript.

```ts
interface Context {
packageJSON: unknown;
}

functiontryGetPackageName(context: Context) {
constpackageJSON = context.packageJSON;
// Check to see if we have an object.
if (packageJSON && typeofpackageJSON === "object") {
// Check to see if it has a string name property.
if ("name"inpackageJSON && typeofpackageJSON.name === "string") {
//                                              ~~~~
// error! Property 'name' does not exist on type 'object.
returnpackageJSON.name;
//                     ~~~~
// error! Property 'name' does not exist on type 'object.
        }
    }

returnundefined;
}
```

This is because while the type of `packageJSON` was narrowed from `unknown` to `object`, the `in` operator strictly narrowed to types that actually defined the property being checked.
As a result, the type of `packageJSON` remained `object`.

TypeScript 4.9 makes the `in` operator a little bit more powerful when narrowing types that *don’t* list the property at all.
Instead of leaving them as-is, the language will intersect their types with `Record<"property-key-being-checked", unknown>`.

So in our example, `packageJSON` will have its type narrowed from `unknown` to `object` to `object & Record<"name", unknown>`
That allows us to access `packageJSON.name` directly and narrow that independently.

```ts
interface Context {
packageJSON: unknown;
}

functiontryGetPackageName(context: Context): string | undefined {
constpackageJSON = context.packageJSON;
// Check to see if we have an object.
if (packageJSON && typeofpackageJSON === "object") {
// Check to see if it has a string name property.
if ("name"inpackageJSON && typeofpackageJSON.name === "string") {
// Just works!
returnpackageJSON.name;
        }
    }

returnundefined;
}
```

TypeScript 4.9 also tightens up a few checks around how `in` is used, ensuring that the left side is assignable to the type `string | number | symbol`, and the right side is assignable to `object`.
This helps check that we’re using valid property keys, and not accidentally checking primitives.

For more information, [read the implementing pull request ↗](https://github.com/microsoft/TypeScript/pull/50666)

## Auto-Accessors in Classes {#auto-accessors-in-classes}

TypeScript 4.9 supports an upcoming feature in ECMAScript called auto-accessors.
Auto-accessors are declared just like properties on classes, except that they’re declared with the `accessor` keyword.

```ts
class Person {
accessorname: string;

constructor(name: string) {
this.name = name;
    }
}
```

Under the covers, these auto-accessors “de-sugar” to a `get` and `set` accessor with an unreachable private property.

```ts
class Person {
#__name: string;

getname() {
returnthis.#__name;
    }
setname(value: string) {
this.#__name = value;
    }

constructor(name: string) {
this.name = name;
    }
}
```

You can [read up more about the auto-accessors pull request on the original PR ↗](https://github.com/microsoft/TypeScript/pull/49705).

## Checks For Equality on `NaN` {#checks-for-equality-on-nan}

A major gotcha for JavaScript developers is checking against the value `NaN` using the built-in equality operators.

For some background, `NaN` is a special numeric value that stands for “Not a Number”.
Nothing is ever equal to `NaN` - even `NaN`!

```js
console.log(NaN == 0)  // false
console.log(NaN === 0) // false

console.log(NaN == NaN)  // false
console.log(NaN === NaN) // false
```

But at least symmetrically *everything* is always not-equal to `NaN`.

```js
console.log(NaN != 0)  // true
console.log(NaN !== 0) // true

console.log(NaN != NaN)  // true
console.log(NaN !== NaN) // true
```

This technically isn’t a JavaScript-specific problem, since any language that contains IEEE-754 floats has the same behavior;
but JavaScript’s primary numeric type is a floating point number, and number parsing in JavaScript can often result in `NaN`.
In turn, checking against `NaN` ends up being fairly common, and the correct way to do so is to use [`Number.isNaN` ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN) - *but* as we mentioned, lots of people accidentally end up checking with `someValue === NaN` instead.

TypeScript now errors on direct comparisons against `NaN`, and will suggest using some variation of `Number.isNaN` instead.

```ts
function validate(someValue: number) {
returnsomeValue !== NaN;
//     ~~~~~~~~~~~~~~~~~
// error: This condition will always return 'true'.
//        Did you mean '!Number.isNaN(someValue)'?
}
```

We believe that this change should strictly help catch beginner errors, similar to how TypeScript currently issues errors on comparisons against object and array literals.

We’d like to extend our thanks to [Oleksandr Tarasiuk ↗](https://github.com/a-tarasyuk) who [contributed this check ↗](https://github.com/microsoft/TypeScript/pull/50626).

## File-Watching Now Uses File System Events {#file-watching-now-uses-file-system-events}

In earlier versions, TypeScript leaned heavily on *polling* for watching individual files.
Using a polling strategy meant checking the state of a file periodically for updates.
On Node.js, [`fs.watchFile` ↗](https://nodejs.org/docs/latest-v18.x/api/fs.html#fswatchfilefilename-options-listener) is the built-in way to get a polling file-watcher.
While polling tends to be more predictable across platforms and file systems, it means that your CPU has to periodically get interrupted and check for updates to the file, even when nothing’s changed.
For a few dozen files, this might not be noticeable;
but on a bigger project with lots of files - or lots of files in `node_modules` - this can become a resource hog.

Generally speaking, a better approach is to use file system events.
Instead of polling, we can announce that we’re interested in updates of specific files and provide a callback for when those files *actually do* change.
Most modern platforms in use provide facilities and APIs like `CreateIoCompletionPort`, `kqueue`, `epoll`, and `inotify`.
Node.js mostly abstracts these away by providing [`fs.watch` ↗](https://nodejs.org/docs/latest-v18.x/api/fs.html#fswatchfilename-options-listener).
File system events usually work great, but there are [lots of caveats ↗](https://nodejs.org/docs/latest-v18.x/api/fs.html#caveats) to using them, and in turn, to using the `fs.watch` API.
A watcher needs to be careful to consider [inode watching ↗](https://nodejs.org/docs/latest-v18.x/api/fs.html#inodes), [unavailability on certain file systems ↗](https://nodejs.org/docs/latest-v18.x/api/fs.html#availability) (e.g.networked file systems), whether recursive file watching is available, whether directory renames trigger events, and even file watcher exhaustion!
In other words, it’s not quite a free lunch, especially if you’re looking for something cross-platform.

As a result, our default was to pick the lowest common denominator: polling.
Not always, but most of the time.

Over time, we’ve provided the means to [choose other file-watching strategies](/typescript/5.1/project-configuration/configuring-watch).
This allowed us to get feedback and harden our file-watching implementation against most of these platform-specific gotchas.
As TypeScript has needed to scale to larger codebases, and has improved in this area, we felt swapping to file system events as the default would be a worthwhile investment.

In TypeScript 4.9, file watching is powered by file system events by default, only falling back to polling if we fail to set up event-based watchers.
For most developers, this should provide a much less resource-intensive experience when running in `--watch` mode, or running with a TypeScript-powered editor like Visual Studio or VS Code.

[The way file-watching works can still be configured](/typescript/5.1/project-configuration/configuring-watch) through environment variables and `watchOptions` - and [some editors like VS Code can support `watchOptions` independently ↗](https://code.visualstudio.com/docs/getstarted/settings#:~:text=typescript%2etsserver%2ewatchOptions).
Developers using more exotic set-ups where source code resides on a networked file systems (like NFS and SMB) may need to opt back into the older behavior; though if a server has reasonable processing power, it might just be better to enable SSH and run TypeScript remotely so that it has direct local file access.
VS Code has plenty of [remote extensions ↗](https://marketplace.visualstudio.com/search?term=remote&target=VSCode&category=All%20categories&sortBy=Relevance) to make this easier.

You can [read up more on this change on GitHub ↗](https://github.com/microsoft/TypeScript/pull/50366).

## “Remove Unused Imports” and “Sort Imports” Commands for Editors {#remove-unused-imports-and-sort-imports-commands-for-editors}

Previously, TypeScript only supported two editor commands to manage imports.
For our examples, take the following code:

```ts
import { Zebra, Moose, HoneyBadger } from "./zoo";
import { foo, bar } from"./helper";

letx: Moose | HoneyBadger = foo();
```

The first was called “Organize Imports” which would remove unused imports, and then sort the remaining ones.
It would rewrite that file to look like this one:

```ts
import { foo } from "./helper";
import { HoneyBadger, Moose } from"./zoo";

letx: Moose | HoneyBadger = foo();
```

In TypeScript 4.3, we introduced a command called “Sort Imports” which would *only* sort imports in the file, but not remove them - and would rewrite the file like this.

```ts
import { bar, foo } from "./helper";
import { HoneyBadger, Moose, Zebra } from"./zoo";

letx: Moose | HoneyBadger = foo();
```

The caveat with “Sort Imports” was that in Visual Studio Code, this feature was only available as an on-save command - not as a manually triggerable command.

TypeScript 4.9 adds the other half, and now provides “Remove Unused Imports”.
TypeScript will now remove unused import names and statements, but will otherwise leave the relative ordering alone.

```ts
import { Moose, HoneyBadger } from "./zoo";
import { foo } from"./helper";

letx: Moose | HoneyBadger = foo();
```

This feature is available to all editors that wish to use either command;
but notably, Visual Studio Code (1.73 and later) will have support built in *and* will surface these commands via its Command Palette.
Users who prefer to use the more granular “Remove Unused Imports” or “Sort Imports” commands should be able to reassign the “Organize Imports” key combination to them if desired.

You can [view specifics of the feature here ↗](https://github.com/microsoft/TypeScript/pull/50931).

## Go-to-Definition on `return` Keywords {#go-to-definition-on-return-keywords}

In the editor, when running a go-to-definition on the `return` keyword, TypeScript will now jump you to the top of the corresponding function.
This can be helpful to get a quick sense of which function a `return` belongs to.

We expect TypeScript will expand this functionality to more keywords [such as `await` and `yield` ↗](https://github.com/microsoft/TypeScript/issues/51223) or [`switch`, `case`, and `default` ↗](https://github.com/microsoft/TypeScript/issues/51225).

[This feature was implemented ↗](https://github.com/microsoft/TypeScript/pull/51227) thanks to [Oleksandr Tarasiuk ↗](https://github.com/a-tarasyuk).

## Performance Improvements {#performance-improvements}

TypeScript has a few small, but notable, performance improvements.

First, TypeScript’s `forEachChild` function has been rewritten to use a function table lookup instead of a `switch` statement across all syntax nodes.
`forEachChild` is a workhorse for traversing syntax nodes in the compiler, and is used heavily in the binding stage of our compiler, along with parts of the language service.
The refactoring of `forEachChild` yielded up to a 20% reduction of time spent in our binding phase and across language service operations.

Once we discovered this performance win for `forEachChild`, we tried it out on `visitEachChild`, a function we use for transforming nodes in the compiler and language service.
The same refactoring yielded up to a 3% reduction in time spent in generating project output.

The initial exploration in `forEachChild` was [inspired by a blog post ↗](https://artemis.sh/2022/08/07/emulating-calculators-fast-in-js.html) by [Artemis Everfree ↗](https://artemis.sh/).
While we have some reason to believe the root cause of our speed-up might have more to do with function size/complexity than the issues described in the blog post, we’re grateful that we were able to learn from the experience and try out a relatively quick refactoring that made TypeScript faster.

Finally, the way TypeScript preserves the information about a type in the true branch of a conditional type has been optimized.
In a type like

```ts
interface Zoo<T extends Animal> {
// ...
}

typeMakeZoo<A> = AextendsAnimal ? Zoo<A> : never;
```

TypeScript has to “remember” that `A` must also be an `Animal` when checking if `Zoo<A>` is valid.
This is basically done by creating a special type that used to hold the intersection of `A` with `Animal`;
however, TypeScript previously did this eagerly which isn’t always necessary.
Furthermore, some faulty code in our type-checker prevented these special types from being simplified.
TypeScript now defers intersecting these types until it’s necessary.
For codebases with heavy use of conditional types, you might witness significant speed-ups with TypeScript, but in our performance testing suite, we saw a more modest 3% reduction in type-checking time.

You can read up more on these optimizations on their respective pull requests:

- [`forEachChild` as a jump-table ↗](https://github.com/microsoft/TypeScript/pull/50225)
- [`visitEachChild` as a jump-table ↗](https://github.com/microsoft/TypeScript/pull/50266)
- [Optimize substitition types ↗](https://github.com/microsoft/TypeScript/pull/50397)

## Correctness Fixes and Breaking Changes {#correctness-fixes-and-breaking-changes}

### `lib.d.ts` Updates {#libdts-updates}

While TypeScript strives to avoid major breaks, even small changes in the built-in libraries can cause issues.
We don’t expect major breaks as a result of DOM and `lib.d.ts` updates, but there may be some small ones.

### Better Types for `Promise.resolve` {#better-types-for-promiseresolve}

`Promise.resolve` now uses the `Awaited` type to unwrap Promise-like types passed to it.
This means that it more often returns the right `Promise` type, but that improved type can break existing code if it was expecting `any` or `unknown` instead of a `Promise`.
For more information, [see the original change ↗](https://github.com/microsoft/TypeScript/pull/33074).

### JavaScript Emit No Longer Elides Imports {#javascript-emit-no-longer-elides-imports}

When TypeScript first supported type-checking and compilation for JavaScript, it accidentally supported a feature called import elision.
In short, if an import is not used as a value, or the compiler can detect that the import doesn’t refer to a value at runtime, the compiler will drop the import during emit.

This behavior was questionable, especially the detection of whether the import doesn’t refer to a value, since it means that TypeScript has to trust sometimes-inaccurate declaration files.
In turn, TypeScript now preserves imports in JavaScript files.

```js
// Input:
import { someValue, SomeClass } from"some-module";

/** @type{SomeClass} */
letval = someValue;

// Previous Output:
import { someValue } from"some-module";

/** @type{SomeClass} */
letval = someValue;

// Current Output:
import { someValue, SomeClass } from"some-module";

/** @type{SomeClass} */
letval = someValue;
```

More information is available at [the implementing change ↗](https://github.com/microsoft/TypeScript/pull/50404).

### `exports` is Prioritized Over `typesVersions` {#exports-is-prioritized-over-typesversions}

Previously, TypeScript incorrectly prioritized the `typesVersions` field over the `exports` field when resolving through a `package.json` under `--moduleResolution node16`.
If this change impacts your library, you may need to add `types@` version selectors in your `package.json`’s `exports` field.

```diff
  {
      "type": "module",
      "main": "./dist/main.js"
      "typesVersions": {
          "<4.8": { ".": ["4.8-types/main.d.ts"] },
          "*": { ".": ["modern-types/main.d.ts"] }
      },
      "exports": {
          ".": {
+             "types@<4.8": "4.8-types/main.d.ts",
+             "types": "modern-types/main.d.ts",
              "import": "./dist/main.js"
          }
      }
  }
```

For more information, [see this pull request ↗](https://github.com/microsoft/TypeScript/pull/50890).

## `substitute` Replaced With `constraint` on `SubstitutionType`s {#substitute-replaced-with-constraint-on-substitutiontypes}

As part of an optimization on substitution types, `SubstitutionType` objects no longer contain the `substitute` property representing the effective substitution (usually an intersection of the base type and the implicit constraint) - instead, they just contain the `constraint` property.

For more details, [read more on the original pull request ↗](https://github.com/microsoft/TypeScript/pull/50397).
