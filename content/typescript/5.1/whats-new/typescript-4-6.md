---
linkTitle: "TypeScript 4.6"
title: "TypeScript: Documentation - TypeScript 4.6"
description: "TypeScript 4.6 Release Notes"
weight: 6
type: docs
canonical: /typescript/5.2/whats-new/typescript-4-6
---

# TypeScript 4.6

## Allowing Code in Constructors Before `super()` {#allowing-code-in-constructors-before-super}

In JavaScript classes it’s mandatory to call `super()` before referring to `this`.
TypeScript enforces this as well, though it was a bit too strict in *how* it ensured this.
In TypeScript, it was previously an error to contain *any* code at the beginning of a constructor if its containing class had any property initializers.

```ts
class Base {
// ...
}

classDerivedextendsBase {
someProperty = true;

constructor() {
// error!
// have to call 'super()' first because it needs to initialize 'someProperty'.
doSomeStuff();
super();
  }
}
```

This made it cheap to check that `super()` gets called before `this` is referenced, but it ended up rejecting a lot of valid code.
TypeScript 4.6 is now much more lenient in that check and permits other code to run before `super()`., all while still ensuring that `super()` occurs at the top-level before any references to `this`.

We’d like to extend our thanks to [Joshua Goldberg ↗](https://github.com/JoshuaKGoldberg) for [patiently working with us to land this change ↗](https://github.com/microsoft/TypeScript/pull/29374)!

## Control Flow Analysis for Destructured Discriminated Unions {#control-flow-analysis-for-destructured-discriminated-unions}

TypeScript is able to narrow types based on what’s called a discriminant property.
For example, in the following code snippet, TypeScript is able to narrow the type of `action` based on every time we check against the value of `kind`.

```ts
type Action =
  | { kind: "NumberContents"; payload: number }
  | { kind: "StringContents"; payload: string };

functionprocessAction(action: Action) {
if (action.kind === "NumberContents") {
// `action.payload` is a number here.
letnum = action.payload * 2;
// ...
  } elseif (action.kind === "StringContents") {
// `action.payload` is a string here.
conststr = action.payload.trim();
// ...
  }
}
```

This lets us work with objects that can hold different data, but a common field tells us *which* data those objects have.

This is very common in TypeScript; however, depending on your preferences, you might have wanted to destructure `kind` and `payload` in the example above.
Perhaps something like the following:

```ts
type Action =
  | { kind: "NumberContents"; payload: number }
  | { kind: "StringContents"; payload: string };

functionprocessAction(action: Action) {
const { kind, payload } = action;
if (kind === "NumberContents") {
letnum = payload * 2;
// ...
  } elseif (kind === "StringContents") {
conststr = payload.trim();
// ...
  }
}
```

Previously TypeScript would error on these - once `kind` and `payload` were extracted from the same object into variables, they were considered totally independent.

In TypeScript 4.6, this just works!

When destructuring individual properties into a `const` declaration, or when destructuring a parameter into variables that are never assigned to, TypeScript will check for if the destructured type is a discriminated union.
If it is, TypeScript can now narrow the types of variables depending on checks of other variables
So in our example, a check on `kind` narrows the type of `payload`.

For more information, [see the pull request that implemented this analysis ↗](https://github.com/microsoft/TypeScript/pull/46266).

## Improved Recursion Depth Checks {#improved-recursion-depth-checks}

TypeScript has some interesting challenges due to the fact that it’s built on a structural type system that also provides generics.

In a structural type system, object types are compatible based on the members they have.

```ts
interface Source {
prop: string;
}

interfaceTarget {
prop: number;
}

functioncheck(source: Source, target: Target) {
target = source;
// error!
// Type 'Source' is not assignable to type 'Target'.
//   Types of property 'prop' are incompatible.
//     Type 'string' is not assignable to type 'number'.
}
```

Notice that whether or not `Source` is compatible with `Target` has to do with whether their *properties* are assignable.
In this case, that’s just `prop`.

When you introduce generics into this, there are some harder questions to answer.
For instance, is a `Source<string>` assignable to a `Target<number>` in the following case?

```ts
interface Source<T> {
prop: Source<Source<T>>;
}

interfaceTarget<T> {
prop: Target<Target<T>>;
}

functioncheck(source: Source<string>, target: Target<number>) {
target = source;
}
```

In order to answer that, TypeScript needs to check whether the types of `prop` are compatible.
That leads to the another question: is a `Source<Source<string>>` assignable to a `Target<Target<number>>`?
To answer that, TypeScript checks whether `prop` is compatible for *those* types, and ends up checking whether `Source<Source<Source<string>>>` is assignable to `Target<Target<Target<number>>>`.
Keep going for a bit, and you might notice that the type infinitely expands the more you dig in.

TypeScript has a few heuristics here - if a type *appears* to be infinitely expanding after encountering a certain depth check, then it considers that the types *could* be compatible.
This is usually enough, but embarrassingly there were some false-negatives that this wouldn’t catch.

```ts
interface Foo<T> {
prop: T;
}

declareletx: Foo<Foo<Foo<Foo<Foo<Foo<string>>>>>>;
declarelety: Foo<Foo<Foo<Foo<Foo<string>>>>>;

x = y;
```

A human reader can see that `x` and `y` should be incompatible in the above example.
While the types are deeply nested, that’s just a consequence of how they were declared.
The heuristic was meant to capture cases where deeply-nested types were generated through exploring the types, not from when a developer wrote that type out themselves.

TypeScript 4.6 is now able to distinguish these cases, and correctly errors on the last example.
Additionally, because the language is no longer concerned with false-positives from explicitly-written types, TypeScript can conclude that a type is infinitely expanding much earlier, and save a bunch of work in checking for type compatibility.
As a result, libraries on DefinitelyTyped like `redux-immutable`, `react-lazylog`, and `yup` saw a 50% reduction in check-time.

You may already have this change because it was cherry-picked into TypeScript 4.5.3, but it is a notable feature of TypeScript 4.6 which you can read up more about [here ↗](https://github.com/microsoft/TypeScript/pull/46599).

## Indexed Access Inference Improvements {#indexed-access-inference-improvements}

TypeScript now can correctly infer to indexed access types which immediately index into a mapped object type.

```ts
interface TypeMap {
number: number;
string: string;
boolean: boolean;
}

typeUnionRecord<PextendskeyofTypeMap> = {
  [KinP]: {
kind: K;
v: TypeMap[K];
f: (p: TypeMap[K]) =>void;
  };
}[P];

functionprocessRecord<KextendskeyofTypeMap>(record: UnionRecord<K>) {
record.f(record.v);
}

// This call used to have issues - now works!
processRecord({
kind:"string",
v:"hello!",

// 'val' used to implicitly have the type 'string | number | boolean',
// but now is correctly inferred to just 'string'.
f: (val) => {
console.log(val.toUpperCase());
  },
});
```

This pattern was already supported and allowed TypeScript to understand that the call to `record.f(record.v)` is valid, but previously the call to `processRecord` would give poor inference results for `val`

TypeScript 4.6 improves this so that no type assertions are necessary within the call to `processRecord`.

For more information, you can [read up on the pull request ↗](https://github.com/microsoft/TypeScript/pull/47109).

## Control Flow Analysis for Dependent Parameters {#control-flow-analysis-for-dependent-parameters}

A signature can be declared with a rest parameter whose type is a discriminated union of tuples.

```ts
function func(...args: ["str", string] | ["num", number]) {
// ...
}
```

What this says is that the arguments to `func` depends entirely on the first argument.
When the first argument is the string `"str"`, then its second argument has to be a `string`.
When its first argument is the string `"num"`, its second argument has to be a `number`.

In cases where TypeScript infers the type of a function from a signature like this, TypeScript can now narrow parameters that depend on each other.

```ts
type Func = (...args: ["a", number] | ["b", string]) => void;

constf1: Func = (kind, payload) => {
if (kind === "a") {
payload.toFixed(); // 'payload' narrowed to 'number'
  }
if (kind === "b") {
payload.toUpperCase(); // 'payload' narrowed to 'string'
  }
};

f1("a", 42);
f1("b", "hello");
```

For more information, [see the change on GitHub ↗](https://github.com/microsoft/TypeScript/pull/47190).

## `--target es2022` {#--target-es2022}

TypeScript’s `--target` option now supports `es2022`.
This means features like class fields now have a stable output target where they can be preserved.
It also means that new built-in functionality like the [`at()` method on `Array`s ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/at), [`Object.hasOwn` ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwn), or [the `cause` option on `new Error` ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Error#rethrowing_an_error_with_a_cause) can be used either with this new `--target` setting, or with `--lib es2022`.

This functionality was [implemented ↗](https://github.com/microsoft/TypeScript/pull/46291) by [Kagami Sascha Rosylight (saschanaz) ↗](https://github.com/saschanaz) over several PRs, and we’re grateful for that contribution!

## Removed Unnecessary Arguments in `react-jsx` {#removed-unnecessary-arguments-in-react-jsx}

Previously, when compiling code like the following in `--jsx react-jsx`

```tsx
export const el = <div>foo</div>;
```

TypeScript would produce the following JavaScript code:

```jsx
import { jsx as _jsx } from "react/jsx-runtime";
exportconstel = _jsx("div", { children:"foo" }, void0);
```

That last `void 0` argument is unnecessary in this emit mode, and removing it can improve bundle sizes.

```diff
- export const el = _jsx("div", { children: "foo" }, void 0);
+ export const el = _jsx("div", { children: "foo" });
```

Thanks to [a pull request ↗](https://github.com/microsoft/TypeScript/pull/47467) from [Alexander Tarasyuk ↗](https://github.com/a-tarasyuk), TypeScript 4.6 now drops the `void 0` argument.

## JSDoc Name Suggestions {#jsdoc-name-suggestions}

In JSDoc, you can document parameters using an `@param` tag.

```js
/**
 * @paramx The first operand
 * @paramy The second operand
 */
functionadd(x, y) {
returnx + y;
}
```

But what happens when these comments fall out of date?
What if we rename `x` and `y` to `a` and `b`?

```js
/**
 * @paramx {number} The first operand
 * @paramy {number} The second operand
 */
functionadd(a, b) {
returna + b;
}
```

Previously TypeScript would only tell you about this when performing type-checking on JavaScript files - when using either the `checkJs` option, or adding a `// @ts-check` comment to the top of your file.

You can now get similar information for TypeScript files in your editor!
TypeScript now provides suggestions for when parameter names don’t match between your function and its JSDoc comment.

![Suggestion diagnostics being shown in the editor for parameter names in JSDoc comments that don't match an actual parameter name.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2022/02/jsdoc-comment-suggestions-4-6.png)

[This change](https://github.com/microsoft/TypeScript/pull/47257) was provided courtesy of [Alexander Tarasyuk](https://github.com/a-tarasyuk)!

## More Syntax and Binding Errors in JavaScript {#more-syntax-and-binding-errors-in-javascript}

TypeScript has expanded its set of syntax and binding errors in JavaScript files.
You’ll see these new errors if you open JavaScript files in an editor like Visual Studio or Visual Studio Code, or if you run JavaScript code through the TypeScript compiler - even if you don’t turn on `checkJs` or add a `// @ts-check` comment to the top of your files.

As one example, if you have two declarations of a `const` in the same scope of a JavaScript file, TypeScript will now issue an error on those declarations.

```ts
const foo = 1234;
//    ~~~
// error: Cannot redeclare block-scoped variable 'foo'.

// ...

constfoo = 5678;
//    ~~~
// error: Cannot redeclare block-scoped variable 'foo'.
```

As another example, TypeScript will let you know if a modifier is being incorrectly used.

```ts
function container() {
exportfunctionfoo() {
//  ~~~~~~
// error: Modifiers cannot appear here.
    }
}
```

These errors can be disabled by adding a `// @ts-nocheck` at the top of your file, but we’re interested in hearing some early feedback about how it works for your JavaScript workflow.
You can easily try it out for Visual Studio Code by installing the [TypeScript and JavaScript Nightly Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next), and read up more on the [first](https://github.com/microsoft/TypeScript/pull/47067) and [second](https://github.com/microsoft/TypeScript/pull/47075) pull requests.

## TypeScript Trace Analyzer {#typescript-trace-analyzer}

Occasionally, teams may encounter types that are computationally expensive to create and compare against other types.
[TypeScript has a `--generateTrace` flag](https://github.com/microsoft/TypeScript/wiki/Performance#performance-tracing) to help identify some of those expensive types, or sometimes help diagnose issues in the TypeScript compiler.
While the information generated by `--generateTrace` can be useful (especially with some information added in TypeScript 4.6), it can often be hard to read in existing trace visualizers.

We recently published a tool called [@typescript/analyze-trace](https://www.npmjs.com/package/@typescript/analyze-trace) to get a more digestible view of this information.
While we don’t expect everyone to need `analyze-trace`, we think it can come in handy for any team that is running into [build performance issues with TypeScript](https://github.com/microsoft/TypeScript/wiki/Performance).

For more information, [see the `analyze-trace` tool’s repo](https://github.com/microsoft/typescript-analyze-trace).

## Breaking Changes {#breaking-changes}

### Object Rests Drop Unspreadable Members from Generic Objects {#object-rests-drop-unspreadable-members-from-generic-objects}

Object rest expressions now drop members that appear to be unspreadable on generic objects.
In the following example…

```ts
class Thing {
someProperty = 42;

someMethod() {
// ...
  }
}

functionfoo<TextendsThing>(x: T) {
let { someProperty, ...rest } = x;

// Used to work, is now an error!
// Property 'someMethod' does not exist on type 'Omit<T, "someProperty" | "someMethod">'.
rest.someMethod();
}
```

the variable `rest` used to have the type `Omit<T, "someProperty">` because TypeScript would strictly analyze which other properties were destructured.
This doesn’t model how `...rest` would work in a destructuring from a non-generic type because `someMethod` would typically be dropped as well.
In TypeScript 4.6, the type of `rest` is `Omit<T, "someProperty" | "someMethod">`.

This can also come up in cases when destructuring from `this`.
When destructuring `this` using a `...rest` element, unspreadable and non-public members are now dropped, which is consistent with destructuring instances of a class in other places.

```ts
class Thing {
someProperty = 42;

someMethod() {
// ...
  }

someOtherMethod() {
let { someProperty, ...rest } = this;

// Used to work, is now an error!
// Property 'someMethod' does not exist on type 'Omit<T, "someProperty" | "someMethod">'.
rest.someMethod();
  }
}
```

For more details, [see the corresponding change here](https://github.com/microsoft/TypeScript/pull/47078).

### JavaScript Files Always Receive Grammar and Binding Errors {#javascript-files-always-receive-grammar-and-binding-errors}

Previously, TypeScript would ignore most grammar errors in JavaScript apart from accidentally using TypeScript syntax in a JavaScript file.
TypeScript now shows JavaScript syntax and binding errors in your file, such as using incorrect modifiers, duplicate declarations, and more.
These will typically be most apparent in Visual Studio Code or Visual Studio, but can also occur when running JavaScript code through the TypeScript compiler.

You can explicitly turn these errors off by inserting a `// @ts-nocheck` comment at the top of your file.

For more information, see the [first](https://github.com/microsoft/TypeScript/pull/47067) and [second](https://github.com/microsoft/TypeScript/pull/47075) implementing pull requests for these features.
