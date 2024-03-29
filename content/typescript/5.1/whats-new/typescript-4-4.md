---
linkTitle: "TypeScript 4.4"
title: "TypeScript: Documentation - TypeScript 4.4"
description: "TypeScript 4.4 Release Notes"
weight: 8
type: docs
canonical: /typescript/5.2/whats-new/typescript-4-4
---

# TypeScript 4.4

## Control Flow Analysis of Aliased Conditions and Discriminants {#control-flow-analysis-of-aliased-conditions-and-discriminants}

In JavaScript, we often have to probe a value in different ways, and do something different once we know more about its type.
TypeScript understands these checks and calls them *type guards*.
Instead of having to convince TypeScript of a variable’s type whenever we use it, the type-checker leverages something called *control flow analysis* to see if we’ve used a type guard before a given piece of code.

For example, we can write something like

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABMOcAUBDATgcwFyLgDWYcA7mAJSIDeAUIojMImlAJ4AOApnC9jkQBeEYgBEAZyhYYYHGOr1GjCAglwANtwB0GuDky5tUOAFVOPLAGEME7mkqUA3A2UB6N8q9eAegH5XAF86QKA)

```ts
function foo(arg: unknown) {
if (typeofarg === "string") {
console.log(arg.toUpperCase());

(parameter) arg: string
  }
}
```

In this example, we checked whether `arg` was a `string`.
TypeScript recognized the `typeof arg === "string"` check, which it considered a type guard, and knew that `arg` was a `string` inside the body of the `if` block.
That let us access `string` methods like `toUpperCase()` without getting an error.

However, what would happen if we moved the condition out to a constant called `argIsString`?

```ts
// In TS 4.3 and below

functionfoo(arg: unknown) {
constargIsString = typeofarg === "string";
if (argIsString) {
console.log(arg.toUpperCase());
//              ~~~~~~~~~~~
// Error! Property 'toUpperCase' does not exist on type 'unknown'.
  }
}
```

In previous versions of TypeScript, this would be an error - even though `argIsString` was assigned the value of a type guard, TypeScript simply lost that information.
That’s unfortunate since we might want to re-use the same check in several places.
To get around that, users often have to repeat themselves or use type assertions (a.k.a. casts).

In TypeScript 4.4, that is no longer the case.
The above example works with no errors!
When TypeScript sees that we are testing a constant value, it will do a little bit of extra work to see if it contains a type guard.
If that type guard operates on a `const`, a `readonly` property, or an un-modified parameter, then TypeScript is able to narrow that value appropriately.

Different sorts of type guard conditions are preserved - not just `typeof` checks.
For example, checks on discriminated unions work like a charm.

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAygFgQ0lAvAKClAPlA3lAawEsA7AEwC4oAiAYyICdaAbCagbigYTKIFcAzlRJ8AtgCMIDKAF8M2PIVKUaAgI58EDNpwFEyEADIQSAc2BxhYydJns0aAGZ8StYEQD2JKFogIAFAKIkFTwSBAAlFYSUnjytF4CwFBEAgDCjCzQKFBB4QB0xOSoKDl0mawc8kSOUP6pGUysEXGYmAD07VAA6tAEJB4A7lCD0IgAbtAIUPRNY1IQAITymNrAfAzeALIIFvkACgCSUABUucEQ+dy8gqdnAEz2mDJQEMwC0LgrUJ09fQPDUYAcm0UFYjmSgyIFh8uQ0vigcAWyzaXAg6025wKegMxjMMJODyesjQMiAA)

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; sideLength: number };

functionarea(shape: Shape): number {
constisCircle = shape.kind === "circle";
if (isCircle) {
// We know we have a circle here!
returnMath.PI * shape.radius ** 2;
  } else {
// We know we're left with a square here!
returnshape.sideLength ** 2;
  }
}
```

Analysis on discriminants in 4.4 also goes a little bit deeper - we can now extract out discriminants and TypeScript can narrow the original object.

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAygFgQ0lAvAKClAPlA3lAawEsA7AEwC4oAiAYyICdaAbCagbigYTKIFcAzlRJ8AtgCMIDKAF8M2PIVKUaAgI58EDNpwFEyEADIQSAc2BxhYydJns0aAGZ8StYEQD2JKFogIAFAKIkFTwSBAAlFYSUnjyAPTxUACiAB7A3G5QHnzAUBbQAOTE5IVQjkQQzGTljALAAHTytF71iiU1MqhQQeH28kSOUP4dqCgoNPRMrNQRcZiYiVAA6tAEJB4A7lCb0IgAbtAIUFMse1IQAITymNrAfAzeALIIFg0ACgCSUABUPcEQBrcXiCX5-ABM9kwXSqAmguBuUCWq0IG22u0K2igrEceU2RAsPh6Gl8UDgF2uCy4EHuj3+4QaegMxjMhJ+EKhsjQMiAA)

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; sideLength: number };

functionarea(shape: Shape): number {
// Extract out the 'kind' field first.
const { kind } = shape;

if (kind === "circle") {
// We know we have a circle here!
returnMath.PI * shape.radius ** 2;
  } else {
// We know we're left with a square here!
returnshape.sideLength ** 2;
  }
}
```

As another example, here’s a function that checks whether two of its inputs have contents.

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABAEzgZTgWwKYGEAW2EA1gM4AUAUIojGAA4hQCCAXIqVAE50DmiAH0Thk2YHWzIANNVoMmAIXaceYfkJFiJ02aXxwQAG2QAROAFEAHtwCGAdThdi7AEZw4h7DbCUAlIgBvWQgETkRMEE4zBydEAF45RhZEADIUxMVU9L0DYzMrWxjiAG5ZGGBEcgiouCL-IJoaAHomxDtsRAhvRBsICGxSUkQAchU+YcR6Ljh6bC5YAcQERDcofBG6JOYJ72QN+SgFYYBCWRoQsDCQelmuZniMlgA6KDgAVRu53BtSbHJfUqNTqhKDCT5cBQPTaKF7vcHfX7-QGNFqIJ7o2QAX0omKAA)

```ts
function doSomeChecks(
inputA: string | undefined,
inputB: string | undefined,
shouldDoExtraWork: boolean
) {
constmustDoWork = inputA && inputB && shouldDoExtraWork;
if (mustDoWork) {
// We can access 'string' properties on both 'inputA' and 'inputB'!
constupperA = inputA.toUpperCase();
constupperB = inputB.toUpperCase();
// ...
  }
}
```

TypeScript can understand that both `inputA` and `inputB` are both present if `mustDoWork` is `true`.
That means we don’t have to write a non-null assertion like `inputA!` to convince TypeScript that `inputA` isn’t `undefined`.

One neat feature here is that this analysis works transitively.
TypeScript will hop through constants to understand what sorts of checks you’ve already performed.

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABMAFADwFyIM5QE4xgDmiAPomCALYBGApnmYjXHADZ0CGYAlIgN4AoRIggJciGNgDK+QiQC8iKAE8ADnTjBEaRAv2IARLgLFDAbmGjxUSdgBy1eoyWqNWnXoOHKtBhasxMAkpWVMiAHk8Rz8XOzD5MnIpGOdLERhtFFC5YijUhj4hERE0SwB6cpEAPQB+KwBfRDo2bDoBK1KKqsQ6xsEGoA)

```ts
function f(x: string | number | boolean) {
constisString = typeofx === "string";
constisNumber = typeofx === "number";
constisStringOrNumber = isString || isNumber;
if (isStringOrNumber) {
x;

(parameter) x: string | number
  } else {
x;

(parameter) x: boolean
  }
}
```

Note that there’s a cutoff - TypeScript doesn’t go arbitrarily deep when checking these conditions, but its analysis is deep enough for most checks.

This feature should make a lot of intuitive JavaScript code “just work” in TypeScript without it getting in your way.
For more details, [check out the implementation on GitHub ↗](https://github.com/microsoft/TypeScript/pull/44730)!

## Symbol and Template String Pattern Index Signatures {#symbol-and-template-string-pattern-index-signatures}

TypeScript lets us describe objects where every property has to have a certain type using *index signatures*.
This allows us to use these objects as dictionary-like types, where we can use string keys to index into them with square brackets.

For example, we can write a type with an index signature that takes `string` keys and maps to `boolean` values.
If we try to assign anything other than a `boolean` value, we’ll get an error.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGY1vRg7AKwBQAlgHYAuMAZgIYDGkoAQnHADaS1kAiJ9FEnDK1oAT1ABvIqFABtANaQxqRBWjkA5gF1UAIzaduAbiIBfIkQAmkeu1FNOFUAFsxfAalYcuvfoOFRMVMiEFAANVp2EitQCjhQWkREEk0yUAMfblAANyiAV0hEIjcPCjkAImo2Cu1QAF446ELTUv9KvVFahtA6dkRIELCAUVgEABpQCrYAB0QK0BJEMgByZ1oMw18S93aKzoAvbsbpuDmK4yA)

```ts
interface BooleanDictionary {
  [key: string]: boolean;
}

declareletmyDict: BooleanDictionary;

// Valid to assign boolean values
myDict["foo"] = true;
myDict["bar"] = false;

// Error, "oops" isn't a boolean
myDict["baz"] = "oops";
```

```text {filename="Generated error"}
Type 'string' is not assignable to type 'boolean'.
```

While [a `Map` might be a better data structure here ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) (specifically, a `Map<string, boolean>`), JavaScript objects are often more convenient to use or just happen to be what we’re given to work with.

Similarly, `Array<T>` already defines a `number` index signature that lets us insert/retrieve values of type `T`.

```ts
// @errors: 2322 2375
// This is part of TypeScript's definition of the built-in Array type.
interfaceArray<T> {
  [index: number]: T;

// ...
}

letarr = newArray<string>();

// Valid
arr[0] = "hello!";

// Error, expecting a 'string' value here
arr[1] = 123;
```

Index signatures are very useful to express lots of code out in the wild;
however, until now they’ve been limited to `string` and `number` keys (and `string` index signatures have an intentional quirk where they can accept `number` keys since they’ll be coerced to strings anyway).
That means that TypeScript didn’t allow indexing objects with `symbol` keys.
TypeScript also couldn’t model an index signature of some *subset* of `string` keys - for example, an index signature which describes just properties whose names start with the text `data-`.

TypeScript 4.4 addresses these limitations, and allows index signatures for `symbol`s and template string patterns.

For example, TypeScript now allows us to declare a type that can be keyed on arbitrary `symbol`s.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGY1vRg7AKwBQAlgHYAuMAZgIYDGkoAwnADYKKgDeRooAbUQBPALaoRogEbsAuqjIBXaTADcRAL5Ei9OGUQVQ0SABNQAXlABlMTLYAKAETGTjgJTrd+wwHNjkMgtrW3YnP0gA9089A1ApNkUmSxtpUMd4xKjtNkhDXQ4kVFYCrktuDXUiEFAAQUREEh8yUQDDOGpQWlAlFWhQEi5aNg4Ad1Mddk4BF1kgtAICdRzDFwA1IaD8qZn1atAAPQB+bS2kAQzIWctHEy6pLpMIx1UgA)

```ts
interface Colors {
  [sym: symbol]: number;
}

constred = Symbol("red");
constgreen = Symbol("green");
constblue = Symbol("blue");

letcolors: Colors = {};

// Assignment of a number is allowed
colors[red] = 255;
letredVal = colors[red];

let redVal: number

colors[blue] = "da ba dee";
```

```text {filename="Generated error"}
Type 'string' is not assignable to type 'number'.
```

Similarly, we can write an index signature with template string pattern type.
One use of this might be to exempt properties starting with `data-` from TypeScript’s excess property checking.
When we pass an object literal to something with an expected type, TypeScript will look for excess properties that weren’t declared in the expected type.

```ts
// @errors: 2322 2375
interfaceOptions {
width?: number;
height?: number;
}

leta: Options = {
width:100,
height:100,

"data-blah":true,
};

interfaceOptionsWithDataPropsextendsOptions {
// Permit any property starting with 'data-'.
    [optName: `data-${string}`]: unknown;
}

letb: OptionsWithDataProps = {
width:100,
height:100,
"data-blah":true,

// Fails for a property which is not known, nor
// starts with 'data-'
"unknown-property":true,
};
```

A final note on index signatures is that they now permit union types, as long as they’re a union of infinite-domain primitive types - specifically:

- `string`
- `number`
- `symbol`
- template string patterns (e.g. ``hello-${string}``)

An index signature whose argument is a union of these types will de-sugar into several different index signatures.

```ts
interface Data {
  [optName: string | symbol]: any;
}

// Equivalent to

interfaceData {
  [optName: string]: any;
  [optName: symbol]: any;
}
```

For more details, [read up on the pull request ↗](https://github.com/microsoft/TypeScript/pull/44512)

## Defaulting to the `unknown` Type in Catch Variables (`--useUnknownInCatchVariables`) {#defaulting-to-the-unknown-type-in-catch-variables---useunknownincatchvariables}

In JavaScript, any type of value can be thrown with `throw` and caught in a `catch` clause.
Because of this, TypeScript historically typed catch clause variables as `any`, and would not allow any other type annotation:

```ts
try {
// Who knows what this might throw...
executeSomeThirdPartyCode();
} catch (err) {
// err: any
console.error(err.message); // Allowed, because 'any'
err.thisWillProbablyFail(); // Allowed, because 'any' :(
}
```

Once TypeScript added the `unknown` type, it became clear that `unknown` was a better choice than `any` in `catch` clause variables for users who want the highest degree of correctness and type-safety, since it narrows better and forces us to test against arbitrary values.
Eventually TypeScript 4.0 allowed users to specify an explicit type annotation of `unknown` (or `any`) on each `catch` clause variable so that we could opt into stricter types on a case-by-case basis;
however, for some, manually specifying `: unknown` on every `catch` clause was a chore.

That’s why TypeScript 4.4 introduces a new flag called [`useUnknownInCatchVariables` ↗](https://www.typescriptlang.org/tsconfig.html#useUnknownInCatchVariables).
This flag changes the default type of `catch` clause variables from `any` to `unknown`.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwFYDsBGUOAOABgBYA2AKABNIBjAGwENpJQAzAVwDtaAXASzhdQkAB50OvSAGU4AW0gAVABb9oVAArNeATwDCcGgAoAlKgBucflQDcFEKAC0z2pOeOKvaDtABvCqAi4q5Ssgoqapra+oaQpnYAvqC0jLy0yqBGMNAmfgGgDtmo3ADWXHAA7lwU+Q4AorAIAISgGvAADjC6oADkCoiIjADmkD2gVHCQiKDlvEH8iHNCoLqdvaXlVT0AdPm0Qohw9JDb2QhZsNv9gyMmdrVgAOoIJYgtj6wpwlzM8BW92TGbHgcnWXDKlS4Y14cF6DXg0B2+X4bEy2VA-C4i0YPEgcFR8IQuX8gUC+yxRxOZ2gF2gVymN0gd3yCQoCSAA)

```ts
try {
executeSomeThirdPartyCode();
} catch (err) {
// err: unknown

// Error! Property 'message' does not exist on type 'unknown'.
console.error(err.message);

// Works! We can narrow 'err' from 'unknown' to 'Error'.
if (errinstanceofError) {
console.error(err.message);
  }
}
```

```text {filename="Generated error"}
'err' is of type 'unknown'.
```

This flag is enabled under the [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict) family of options.
That means that if you check your code using [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict), this option will automatically be turned on.
You may end up with errors in TypeScript 4.4 such as

```
Property 'message' does not exist on type 'unknown'.
Property 'name' does not exist on type 'unknown'.
Property 'stack' does not exist on type 'unknown'.
```

In cases where we don’t want to deal with an `unknown` variable in a `catch` clause, we can always add an explicit `: any` annotation so that we can opt *out* of stricter types.

[Try this code ↗](https://www.typescriptlang.org/play#code/CYUwxgNghgTiAEAzArgOzAFwJYHtXxAA9xkMQBlHAWxABUALLGYABVgwE8BhHUACgCUALngA3HFmABuAFAB6OfAC0KsKRVKZGGB3gBvGfALE1ZSjQZNW7brxCDZAX3hgoGMPXh8QMGCKioHAL6hi54AM44ECAAdD4wODDevjE04eFQAOYgAlLwCvAA6okA1uHwWVBYqACEMo5AA)

```ts
try {
executeSomeThirdPartyCode();
} catch (err: any) {
console.error(err.message); // Works again!
}
```

For more information, take a look at [the implementing pull request ↗](https://github.com/microsoft/TypeScript/pull/41013).

## Exact Optional Property Types (`--exactOptionalPropertyTypes`) {#exact-optional-property-types---exactoptionalpropertytypes}

In JavaScript, reading a *missing* property on an object produces the value `undefined`.
It’s also possible to *have* an actual property with the value `undefined`.
A lot of code in JavaScript tends to treat these situations the same way, and so initially TypeScript just interpreted every optional property as if a user had written `undefined` in the type.
For example,

```ts
interface Person {
name: string;
age?: number;
}
```

was considered equivalent to

```ts
interface Person {
name: string;
age?: number | undefined;
}
```

What this meant is that a user could explicitly write `undefined` in place of `age`.

```ts
const p: Person = {
name:"Daniel",
age:undefined, // This is okay by default.
};
```

So by default, TypeScript doesn’t distinguish between a present property with the value `undefined` and a missing property.
While this works most of the time, not all code in JavaScript makes the same assumptions.
Functions and operators like `Object.assign`, `Object.keys`, object spread (`{ ...obj }`), and `for`-`in` loops behave differently depending on whether or not a property actually exists on an object.
In the case of our `Person` example, this could potentially lead to runtime errors if the `age` property was observed in a context where its presence was important.

In TypeScript 4.4, the new flag [`exactOptionalPropertyTypes` ↗](https://www.typescriptlang.org/tsconfig.html#exactOptionalPropertyTypes) specifies that optional property types should be interpreted exactly as written, meaning that `| undefined` is not added to the type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMA8EMGMAuB5ADoglgewHawDYAKATlqpMYgJ4AqV5AzgFAgQWnEMBcoATAMy9effgHYArEww5EFAGYJIoQhQa5QAbyahQeALaQeDRMWkBzANzbQsM5AD8PHAFc9AIwpWAvizABaAPhnRAC-X1AAdQxEAAtQAHIYBBR0bDwiUnJKWnpIBnjQXC4meFxjUFQeFU51AF5Na31DUAAiABFYHAxIfBaAGmtbZuccABNIOWlIUb7QVgBRYg4AQlAR8cmcadAMBhx4xBtdVw9iJi8LIA)

```ts
// With 'exactOptionalPropertyTypes' on:
constp: Person = {
name:"Daniel",
age:undefined, // Error! undefined isn't a number
};
```

```text {filename="Generated error"}
Type '{ name: string; age: undefined; }' is not assignable to type 'Person' with 'exactOptionalPropertyTypes: true'. Consider adding 'undefined' to the types of the target's properties.
  Types of property 'age' are incompatible.
    Type 'undefined' is not assignable to type 'number'.
```

This flag is **not** part of the [`strict` ↗](https://www.typescriptlang.org/tsconfig.html#strict) family and needs to be turned on explicitly if you’d like this behavior.
It also requires [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) to be enabled as well.
We’ve been making updates to DefinitelyTyped and other definitions to try to make the transition as straightforward as possible, but you may encounter some friction with this depending on how your code is structured.

For more information, you can [take a look at the implementing pull request here ↗](https://github.com/microsoft/TypeScript/pull/43947).

## `static` Blocks in Classes {#static-blocks-in-classes}

TypeScript 4.4 brings support for [`static` blocks in classes ↗](https://github.com/tc39/proposal-class-static-block#ecmascript-class-static-initialization-blocks), an upcoming ECMAScript feature that can help you write more-complex initialization code for static members.

[Try this code ↗](https://www.typescriptlang.org/play#code/CYUwxgNghgTiAEAzArgOzAFwJYHtXwGccBbEAYT2C2zwAoBKALngCMccIQpUAoAej7wAtCLDIMIoT0hQCBeADF28AN494GwhijYw8MDjQZ4AXngAGANw91mgfAAqACyzzX8KFp1Y9LCDjAAa0ZbDQJtXVVQzQ0sRHhaIlIKVCoaVAZ6KJiczSUcADoDIwBqEutcjQBfaJqqoA)

```ts
class Foo {
staticcount = 0;

// This is a static block:
static {
if (someCondition()) {
Foo.count++;
        }
    }
}
```

These static blocks allow you to write a sequence of statements with their own scope that can access private fields within the containing class.
That means that we can write initialization code with all the capabilities of writing statements, no leakage of variables, and full access to our class’s internals.

[Try this code ↗](https://www.typescriptlang.org/play#code/CYUwxgNghgTiAEAzArgOzAFwJYHtXwhymABkoBnDASVUqnRHIAoBKALnnoE8BtAXQBQAeiHwAtBLDIMEsQMgVy8AGI4c8AN4D4O+HWxh4AYjA40GeAF54ABgDcA7boDmIC6fOtNT3brgZkGHxVHAA6EzNUDAdfAF9HX30sQy1fXwwYLm80nPhTWgtoSho6BiVrQmIyYoL6MEZWGNzfEPCPKPgAagqKalqy0IgQVGcMAAsmnPjmsCgMMDHNad142KA)

```ts
class Foo {
static#count = 0;

getcount() {
returnFoo.#count;
    }

static {
try {
constlastInstances = loadLastInstances();
Foo.#count += lastInstances.length;
        }
catch {}
    }
}
```

Without `static` blocks, writing the code above was possible, but often involved several different types of hacks that had to compromise in some way.

Note that a class can have multiple `static` blocks, and they’re run in the same order in which they’re written.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAUCcEsDsBcDOAuAUCUnQEZ1iwEx5agDMqAxgDYCGiioAYgPbOgDeqJi8N80FUAAdIzIaAC8OLlh58BHGSUwVmsRMyoBTAHRVmAcwAULZjpFiA1JYCUAbiWgAvo7n9BnZVlXrNu-cam5qJC1vaOLty87opeKmoa2nqGJqzBVrYOJC5OQA)

```ts
// Prints:
//    1
//    2
//    3
classFoo {
staticprop = 1
static {
console.log(Foo.prop++);
    }
static {
console.log(Foo.prop++);
    }
static {
console.log(Foo.prop++);
    }
}
```

We’d like to extend our thanks to [Wenlu Wang ↗](https://github.com/Kingwl) for TypeScript’s implementation of this feature.
For more details, you can [see that pull request here ↗](https://github.com/microsoft/TypeScript/pull/43370).

## `tsc --help` Updates and Improvements {#tsc---help-updates-and-improvements}

TypeScript’s `--help` option has gotten a refresh!
Thanks to work in part by [Song Gao ↗](https://github.com/ShuiRuTian), we’ve brought in changes to [update the descriptions of our compiler options ↗](https://github.com/microsoft/TypeScript/pull/44409) and [restyle the `--help` menu ↗](https://github.com/microsoft/TypeScript/pull/44157) with colors and other visual separation.

![The new TypeScript --help menu where the output is bucketed into several different areas](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2021/08/tsc-help-ps-wt-4-4.png)

You can read more on [the original proposal thread](https://github.com/microsoft/TypeScript/issues/44074).

## Performance Improvements {#performance-improvements}

### Faster Declaration Emit {#faster-declaration-emit}

TypeScript now caches whether internal symbols are accessible in different contexts, along with how specific types should be printed.
These changes can improve TypeScript’s general performance in code with fairly complex types, and is especially observed when emitting `.d.ts` files under the [`declaration`](/tsconfig.html#declaration) flag.

[See more details here](https://github.com/microsoft/TypeScript/pull/43973).

### Faster Path Normalization {#faster-path-normalization}

TypeScript often has to do several types of “normalization” on file paths to get them into a consistent format that the compiler can use everywhere.
This involves things like replacing backslashes with slashes, or removing intermediate `/./` and `/../` segments of paths.
When TypeScript has to operate over millions of these paths, these operations end up being a bit slow.
In TypeScript 4.4, paths first undergo quick checks to see whether they need any normalization in the first place.
These improvements together reduce project load time by 5-10% on bigger projects, and significantly more in massive projects that we’ve tested internally.

For more details, you can [view the PR for path segment normalization](https://github.com/microsoft/TypeScript/pull/44173) along with [the PR for slash normalization](https://github.com/microsoft/TypeScript/pull/44100).

### Faster Path Mapping {#faster-path-mapping}

TypeScript now caches the way it constructs path-mappings (using the [`paths`](/tsconfig.html#paths) option in `tsconfig.json`).
For projects with several hundred mappings, the reduction is significant.
You can see more [on the change itself](https://github.com/microsoft/TypeScript/pull/44078).

### Faster Incremental Builds with `--strict` {#faster-incremental-builds-with---strict}

In what was effectively a bug, TypeScript would end up redoing type-checking work under [`incremental`](/tsconfig.html#incremental) compilations if [`strict`](/tsconfig.html#strict) was on.
This led to many builds being just as slow as if [`incremental`](/tsconfig.html#incremental) was turned off.
TypeScript 4.4 fixes this, though the change has also been back-ported to TypeScript 4.3.

See more [here](https://github.com/microsoft/TypeScript/pull/44394).

### Faster Source Map Generation for Big Outputs {#faster-source-map-generation-for-big-outputs}

TypeScript 4.4 adds an optimization for source map generation on extremely large output files.
When building an older version of the TypeScript compiler, this results in around an 8% reduction in emit time.

We’d like to extend our thanks to [David Michon](https://github.com/dmichon-msft) who provided a [simple and clean change](https://github.com/microsoft/TypeScript/pull/44031) to enable this performance win.

### Faster `--force` Builds {#faster---force-builds}

When using `--build` mode on project references, TypeScript has to perform up-to-date checks to determine which files need to be rebuilt.
When performing a [`--force`](/tsconfig.html#force) build, however, that information is irrelevant since every project dependency will be rebuilt from scratch.
In TypeScript 4.4, [`--force`](/tsconfig.html#force) builds avoid those unnecessary steps and start a full build.
See more about the change [here](https://github.com/microsoft/TypeScript/pull/43666).

## Spelling Suggestions for JavaScript {#spelling-suggestions-for-javascript}

TypeScript powers the JavaScript editing experience in editors like Visual Studio and Visual Studio Code.
Most of the time, TypeScript tries to stay out of the way in JavaScript files;
however, TypeScript often has a lot of information to make confident suggestions, and ways of surfacing suggestions that aren’t *too* invasive.

That’s why TypeScript now issues spelling suggestions in plain JavaScript files - ones without `// @ts-check` or in a project with [`checkJs`](/tsconfig.html#checkJs) turned off.
These are the same *“Did you mean…?”* suggestions that TypeScript files already have, and now they’re available in *all* JavaScript files in some form.

These spelling suggestions can provide a subtle clue that your code is wrong.
We managed to find a few bugs in existing code while testing this feature!

For more details on this new feature, [take a look at the pull request](https://github.com/microsoft/TypeScript/pull/44271)!

## Inlay Hints {#inlay-hints}

TypeScript 4.4 provides support for *inlay hints* which can help display useful information like parameter names and return types in your code.
You can think of it as a sort of friendly “ghost text”.

![A preview of inlay hints in Visual Studio Code](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2021/08/inlayHints-4.4-rc-ghd.png)

This feature was built by [Wenlu Wang](https://github.com/Kingwl) whose [pull request](https://github.com/microsoft/TypeScript/pull/42089) has more details.

Wenlu also contributed [the integration for inlay hints in Visual Studio Code](https://github.com/microsoft/vscode/pull/113412) which has shipped as [part of the July 2021 (1.59) release](https://code.visualstudio.com/updates/v1_59#_typescript-44).
If you’d like to try inlay hints out, make sure you’re using a recent [stable](https://code.visualstudio.com/updates/v1_59) or [insiders](https://code.visualstudio.com/insiders/) version of the editor.
You can also modify when and where inlay hints get displayed in Visual Studio Code’s settings.

## Auto-Imports Show True Paths in Completion Lists {#auto-imports-show-true-paths-in-completion-lists}

When editors like Visual Studio Code show a completion list, completions which include auto-imports are displayed with a path to the given module;
however, this path usually isn’t what TypeScript ends up placing in a module specifier.
The path is usually something relative to the *workspace*, meaning that if you’re importing from a package like `moment`, you’ll often see a path like `node_modules/moment`.

![A completion list containing unwieldy paths containing 'node_modules'. For example, the label for 'calendarFormat' is 'node_modules/moment/moment' instead of 'moment'.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2021/08/completion-import-labels-pre-4-4.png)

These paths end up being unwieldy and often misleading, especially given that the path that actually gets inserted into your file needs to consider Node’s `node_modules` resolution, path mappings, symlinks, and re-exports.

That’s why with TypeScript 4.4, the completion item label now shows the *actual* module path that will be used for the import!

![A completion list containing clean paths with no intermediate 'node_modules'. For example, the label for 'calendarFormat' is 'moment' instead of 'node_modules/moment/moment'.](/assets/typescript/5.1/external/devblogs.microsoft.com/typescript/wp-content/uploads/sites/11/2021/08/completion-import-labels-4-4.png)

Since this calculation can be expensive, completion lists containing many auto-imports may fill in the final module specifiers in batches as you type more characters. It’s possible that you’ll still sometimes see the old workspace-relative path labels; however, as your editing experience “warms up”, they should get replaced with the actual path after another keystroke or two.

## Breaking Changes {#breaking-changes}

### `lib.d.ts` Changes for TypeScript 4.4 {#libdts-changes-for-typescript-44}

As with every TypeScript version, declarations for `lib.d.ts` (especially the declarations generated for web contexts), have changed.
You can consult [our list of known `lib.dom.d.ts` changes](https://github.com/microsoft/TypeScript-DOM-lib-generator/issues/1029#issuecomment-869224737) to understand what is impacted.

### More-Compliant Indirect Calls for Imported Functions {#more-compliant-indirect-calls-for-imported-functions}

In earlier versions of TypeScript, calling an import from CommonJS, AMD, and other non-ES module systems would set the `this` value of the called function.
Specifically, in the following example, when calling `fooModule.foo()`, the `foo()` method will have `fooModule` set as the value of `this`.

```ts
// Imagine this is our imported module, and it has an export named 'foo'.
letfooModule = {
foo() {
console.log(this);
  },
};

fooModule.foo();
```

This is not the way exported functions in ECMAScript are supposed to work when we call them.
That’s why TypeScript 4.4 intentionally discards the `this` value when calling imported functions, by using the following emit.

```ts
// Imagine this is our imported module, and it has an export named 'foo'.
letfooModule = {
foo() {
console.log(this);
  },
};

// Notice we're actually calling '(0, fooModule.foo)' now, which is subtly different.
(0, fooModule.foo)();
```

You can [read up more about the changes here](https://github.com/microsoft/TypeScript/pull/44624).

### Using `unknown` in Catch Variables {#using-unknown-in-catch-variables}

Users running with the [`strict`](/tsconfig.html#strict) flag may see new errors around `catch` variables being `unknown`, especially if the existing code assumes only `Error` values have been caught.
This often results in error messages such as:

```
Property 'message' does not exist on type 'unknown'.
Property 'name' does not exist on type 'unknown'.
Property 'stack' does not exist on type 'unknown'.
```

To get around this, you can specifically add runtime checks to ensure that the thrown type matches your expected type.
Otherwise, you can just use a type assertion, add an explicit `: any` to your catch variable, or turn off [`useUnknownInCatchVariables`](/tsconfig.html#useUnknownInCatchVariables).

### Broader Always-Truthy Promise Checks {#broader-always-truthy-promise-checks}

In prior versions, TypeScript introduced “Always Truthy Promise checks” to catch code where an `await` may have been forgotten;
however, the checks only applied to named declarations.
That meant that while this code would correctly receive an error…

```ts
async function foo(): Promise<boolean> {
returnfalse;
}

asyncfunctionbar(): Promise<string> {
constfooResult = foo();
if (fooResult) {
// <- error! :D
return"true";
  }
return"false";
}
```

…the following code would not.

```ts
async function foo(): Promise<boolean> {
returnfalse;
}

asyncfunctionbar(): Promise<string> {
if (foo()) {
// <- no error :(
return"true";
  }
return"false";
}
```

TypeScript 4.4 now flags both.
For more information, [read up on the original change](https://github.com/microsoft/TypeScript/pull/44491).

### Abstract Properties Do Not Allow Initializers {#abstract-properties-do-not-allow-initializers}

The following code is now an error because abstract properties may not have initializers:

```ts
abstract class C {
abstractprop = 1;
//       ~~~~
// Property 'prop' cannot have an initializer because it is marked abstract.
}
```

Instead, you may only specify a type for the property:

```ts
abstract class C {
abstractprop: number;
}
```
