---
linkTitle: "Enums"
title: "TypeScript: Handbook - Enums"
description: "How TypeScript enums work"
weight: 5
type: docs
canonical: /typescript/5.2/reference/enums
---

# Enums

## Numeric enums {#numeric-enums}

We’ll first start off with numeric enums, which are probably more familiar if you’re coming from other languages.
An enum can be defined using the `enum` keyword.

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAIglgJ2AYwC5wPYigbwFBRQCqADlALxQCMANAbBgO4h2EAywAZqq1AEpwA5gAseeAL5A)

```ts
enum Direction {
Up = 1,
Down,
Left,
Right,
}
```

Above, we have a numeric enum where `Up` is initialized with `1`.
All of the following members are auto-incremented from that point on.
In other words, `Direction.Up` has the value `1`, `Down` has `2`, `Left` has `3`, and `Right` has `4`.

If we wanted, we could leave off the initializers entirely:

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAIglgJ2AYwC5wPYigbwFBRQCqADgDQGwYDuIFhAMsAGar1QBKcA5gBZt4AvkA)

```ts
enum Direction {
Up,
Down,
Left,
Right,
}
```

Here, `Up` would have the value `0`, `Down` would have `1`, etc.
This auto-incrementing behavior is useful for cases where we might not care about the member values themselves, but do care that each value is distinct from other values in the same enum.

Using an enum is simple: just access any member as a property off of the enum itself, and declare types using the name of the enum:

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAqgzsATgJWHADgexAqBvAKCigDlMoBeKABgBoioBNNSqARnoF8CCAzMEAGMALgEtsURGiwgAJgAopg0elGhhALihxhiUSADmtKBDRwAhgeBb4SVBmwIAlFoBumUbPwMA9D6gAdEEE3ARSDnLyAEQACnpCZlAAwuaImAA2+sBRxrYo0o7AAcxwTgDcQA)

```ts
enum UserResponse {
No = 0,
Yes = 1,
}

functionrespond(recipient: string, message: UserResponse): void {
// ...
}

respond("Princess Caroline", UserResponse.Yes);
```

Numeric enums can be mixed in [computed and constant members (see below)](/typescript/5.1/reference/enums#computed-and-constant-members).
The short story is, enums without initializers either need to be first, or have to come after numeric enums initialized with numeric constants or other constant enum members.
In other words, the following isn’t allowed:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygIwAYBsaBQBjOAO0QBdQBzSUgZTgFtIA1AQwBsBXSUAXlAAoAlLwB8oAEwBmANy4QoALRL8HUkoW5IRDvVABRUAG9coUAEFelanUatOkIQBoToAELOAvkA)

```ts
enum E {
A = getSomeValue(),
B,
}
```

```text {filename="Generated error"}
Enum member must have initializer.
```

## String enums {#string-enums}

String enums are a similar concept, but have some subtle [runtime differences](/typescript/5.1/reference/enums#enums-at-runtime) as documented below.
In a string enum, each member has to be constant-initialized with a string literal, or with another string enum member.

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAIglgJ2AYwC5wPYigbwFBRQCqADlALxQBERAClQDQGwYDu2lVMA8gOoByjZgBlgAM1QVqwgKIAxACpDCAJTgBzABaTOKgJIBxABJKmAXyA)

```ts
enum Direction {
Up = "UP",
Down = "DOWN",
Left = "LEFT",
Right = "RIGHT",
}
```

While string enums don’t have auto-incrementing behavior, string enums have the benefit that they “serialize” well.
In other words, if you were debugging and had to read the runtime value of a numeric enum, the value is often opaque - it doesn’t convey any useful meaning on its own (though [reverse mapping](/typescript/5.1/reference/enums#reverse-mappings) can often help). String enums allow you to give a meaningful and readable value when your code runs, independent of the name of the enum member itself.

## Heterogeneous enums {#heterogeneous-enums}

Technically enums can be mixed with string and numeric members, but it’s not clear why you would ever want to do so:

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAQg9nANsAhiAMgSwNbABLAAuwATnAOajBxgDOAouNAN4BQUUAcnFALxQAGADTsoATWC0+UAERj6AZRkiAvkA)

```ts
enum BooleanLikeHeterogeneousEnum {
No = 0,
Yes = "YES",
}
```

Unless you’re really trying to take advantage of JavaScript’s runtime behavior in a clever way, it’s advised that you don’t do this.

## Computed and constant members {#computed-and-constant-members}

Each enum member has a value associated with it which can be either *constant* or *computed*.
An enum member is considered constant if:

- It is the first member in the enum and it has no initializer, in which case it’s assigned the value `0`:
  [Try this code ↗](https://www.typescriptlang.org/play#code/PTAEFEDoA1QSwM6gMYHsB2CAuBDdWAuAKAFN0BXAWwlAG8jRRoAaIgXyA)


  ```ts
  // E.X is constant:
  enumE {
  X,
  }
  ```

- It does not have an initializer and the preceding enum member was a *numeric* constant.
  In this case the value of the current enum member will be the value of the preceding enum member plus one.
  [Try this code ↗](https://www.typescriptlang.org/play#code/PTAEEEBtNBTA7ArgW1M2yBGsBOBnUAS3lAHIBRARlNAEN4ATM8gJhtp1lAGMB7ePABd6ggHQAocQhSgqoAN7jQoABoAaJaACaG5QC0NAX0nTUrBZvCgAvKEq7QAIQcBhI0A)


  ```ts
  // All enum members in 'E1' and 'E2' are constant.

  enumE1 {
  X,
  Y,
  Z,
  }

  enumE2 {
  A = 1,
  B,
  C,
  }
  ```

- The enum member is initialized with a constant enum expression.
  A constant enum expression is a subset of TypeScript expressions that can be fully evaluated at compile time.
  An expression is a constant enum expression if it is:
  1. a literal enum expression (basically a string literal or a numeric literal)
  2. a reference to previously defined constant enum member (which can originate from a different enum)
  3. a parenthesized constant enum expression
  4. one of the `+`, `-`, `~` unary operators applied to constant enum expression
  5. `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^` binary operators with constant enum expressions as operands
  It is a compile time error for constant enum expressions to be evaluated to `NaN` or `Infinity`.

In all other cases enum member is considered computed.

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAYglgG2AQQMauAZ01A3gKCigHpipUB7ETAFwEMQaoJgIAjYAJ00KgDkqwADS8ASsDoATKAF4oARigAeJQpFEA6pzg1gshctUAmdVHFStOvXPPSAPlEu7TpchQgAHMLukt2XXgBxfQAieSMAZhCAOiQQAHMaAAsRAF8gA)

```ts
enum FileAccess {
// constant members
None,
Read = 1 << 1,
Write = 1 << 2,
ReadWrite = Read | Write,
// computed member
G = "123".length,
}
```

## Union enums and enum member types {#union-enums-and-enum-member-types}

There is a special subset of constant enum members that aren’t calculated: literal enum members.
A literal enum member is a constant enum member with no initialized value, or with values that are initialized to

- any string literal (e.g. `"foo"`, `"bar"`, `"baz"`)
- any numeric literal (e.g. `1`, `100`)
- a unary minus applied to any numeric literal (e.g. `-1`, `-100`)

When all members in an enum have literal enum values, some special semantics come into play.

The first is that enum members also become types as well!
For example, we can say that certain members can *only* have the value of an enum member:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGY1oFCQHYCuAtqAMoAWAhgA6QDSAlvgCagDeOooAwo9AGMANpAA0XcgEdCVaGJwBfHDmYAXGADMqAyL37DdnbgGtmLVJVoMzAOj6CRAbgnQqLRoRSgixAEYxnJRV8dWgtHSkZOQ4JU1YLajomVhsyaVlIZ25ERhZIABkCAHNVClQff2hA5RFVUAFUewNQAF4YkzMEq2SWVPS5cW5Xd09UAEYABgnxBUcgA)

```ts
enum ShapeKind {
Circle,
Square,
}

interfaceCircle {
kind: ShapeKind.Circle;
radius: number;
}

interfaceSquare {
kind: ShapeKind.Square;
sideLength: number;
}

letc: Circle = {
kind:ShapeKind.Square,
radius:100,
};
```

```text {filename="Generated error"}
Type 'ShapeKind.Square' is not assignable to type 'ShapeKind.Circle'.
```

The other change is that enum types themselves effectively become a *union* of each enum member.
With union enums, the type system is able to leverage the fact that it knows the exact set of values that exist in the enum itself.
Because of that, TypeScript can catch bugs where we might be comparing values incorrectly.
For example:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGYBsB2AUJAHYCuAtqAKKgDeeooAYnHADR2gBCAhtGwL548AM2KEAxgBcAlnEKghACgAeqCgEoa7KUNDLQAQgC8hygDomcUAB8roJQeNnu0DbXr0Q7AXyA)

```ts
enum E {
Foo,
Bar,
}

functionf(x: E) {
if (x !== E.Foo || x !== E.Bar) {
//
  }
}
```

```text {filename="Generated error"}
This comparison appears to be unintentional because the types 'E.Foo' and 'E.Bar' have no overlap.
```

In that example, we first checked whether `x` was *not*`E.Foo`.
If that check succeeds, then our `||` will short-circuit, and the body of the ‘if’ will run.
However, if the check didn’t succeed, then `x` can *only* be `E.Foo`, so it doesn’t make sense to see whether it’s *not* equal to `E.Bar`.

## Enums at runtime {#enums-at-runtime}

Enums are real objects that exist at runtime.
For example, the following enum

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAolDeAoKUAaAaZUCamUC1MBfIA)

```ts
enum E {
X,
Y,
Z,
}
```

can actually be passed around to functions

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAolDeAoKUAaAaZUCamUC1MBfRRAMzBAGMAXASwHsQoyAKBgIwCsAuBNPuAgdgAJyhEAlAiyjgNMKOacuAOlQBuRCUQB6XVADqDUQGsAzuijm61YFADkMB1AAWAQ3NR3UAA6iGXzEaAE8oEHcIYAATR1QXAHdXOipXKDovHyERUVVyVhhJDSA)

```ts
enum E {
X,
Y,
Z,
}

functionf(obj: { X: number }) {
returnobj.X;
}

// Works, since 'E' has a property named 'X' which is a number.
f(E);
```

## Enums at compile time {#enums-at-compile-time}

Even though Enums are real objects that exist at runtime, the `keyof` keyword works differently than you might expect for typical objects. Instead, use `keyof typeof` to get a Type that represents all Enum keys as strings.

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAMg9gcxsAbsANlA3gKClAUQCUiB5IgGjygHUBBIgOSvwElGAxUlqAEQIBCAVQDiVAL44cAegBUsvLKgAVABYBLAM5QtUYAEcw6lAEN0oAC5QLcAFyLrATwAOwWImRp0AZQsAndRAEbQBeKAByYjIicKgAHwj6JliE8PYuFIj+YRFwgG5FaRwLFzd4JFQMXwCg0KgAa2BHOAAzJ1dW9wqvApwWsBAAYwt1OBAoZxqLVghnOD8LExALAApGx1suzyr-QOCKKAhgTU0TBGBNzV2ggEpsakGxq6hwaDDy7fQAbXWAXQL8Oo2itXlAADzvDyVdAAOiSjDuuHw+EeIE0cHMMPQiBWACJylBzF4Gk0dJpbLiDusbgDkaj0ZjsQg8QSiRgoKZ0GA3FoKQdXjTqCinhjgFicfjEIToYdjqdzmS+bKTmdgIL8JJJJNAtNZvNFss8VFyJSoLi1LpdCZlfLgLiaUA)

```ts
enum LogLevel {
ERROR,
WARN,
INFO,
DEBUG,
}

/**
 * This is equivalent to:
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
typeLogLevelStrings = keyoftypeofLogLevel;

functionprintImportant(key: LogLevelStrings, message: string) {
constnum = LogLevel[key];
if (num <= LogLevel.WARN) {
console.log("Log level key is:", key);
console.log("Log level value is:", num);
console.log("Log level message is:", message);
  }
}
printImportant("ERROR", "This is a message");
```

### Reverse mappings {#reverse-mappings}

In addition to creating an object with property names for members, numeric enums members also get a *reverse mapping* from enum values to enum names.
For example, in this example:

[Try this code ↗](https://www.typescriptlang.org/play#code/KYOwrgtgBAou0G8BQUoEEA0SC+SkBtgAXKAQygF5Z4A6NAbgOKhFImAHkAzNS6yANqkAuvSgB6cVABEaaUA)

```ts
enum Enum {
A,
}

leta = Enum.A;
letnameOfA = Enum[a]; // "A"
```

TypeScript compiles this down to the following JavaScript:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGcAsHsHcCiBbAlgFwFAFMB2BXZURAogb01FAEEAaTAX00wBtt1QBDUAXmNIB01ANyt2oXJ2TYA8gDNqvfoQDanALrDQIUACJquoA)

```ts
"use strict";
varEnum;
(function (Enum) {
Enum[Enum["A"] = 0] = "A";
})(Enum || (Enum = {}));
leta = Enum.A;
letnameOfA = Enum[a]; // "A"
```

In this generated code, an enum is compiled into an object that stores both forward (`name` -> `value`) and reverse (`value` -> `name`) mappings.
References to other enum members are always emitted as property accesses and never inlined.

Keep in mind that string enum members *do not* get a reverse mapping generated at all.

### `const` enums {#const-enums}

In most cases, enums are a perfectly valid solution.
However sometimes requirements are tighter.
To avoid paying the cost of extra generated code and additional indirection when accessing enum values, it’s possible to use `const` enums.
Const enums are defined using the `const` modifier on our enums:

[Try this code ↗](https://www.typescriptlang.org/play#code/MYewdgzgLgBApmArgWxgUSag3gKBjAQRgF4YBGAGjxgCETCYAqGAJioF8g)

```ts
const enum Enum {
A = 1,
B = A * 2,
}
```

Const enums can only use constant enum expressions and unlike regular enums they are completely removed during compilation.
Const enum members are inlined at use sites.
This is possible since const enums cannot have computed members.

[Try this code ↗](https://www.typescriptlang.org/play#code/MYewdgzgLgBApmArgWxgEQJYCc7Ch8GAbwCgYYBVABwBoz0QB3MO8gGTgDMpWYAlDAHMAFjxIBfEiQA2cWABNsufOAgwAvDADa9TDjwEwAOmq89yw0bRMWupQfBGO3M-ZXGBIsQF0A3EA)

```ts
const enum Direction {
Up,
Down,
Left,
Right,
}

letdirections = [
Direction.Up,
Direction.Down,
Direction.Left,
Direction.Right,
];
```

in generated code will become

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGcAsHsHcCiBbAlgFwFAGNYDtJ1QBTPAV2VABFUAnY7dVfUAb01FAFUAHAGg7UEeAZwAyxAGbpRoAEqoA5tBmYAvpkwAbYkQAmdBk3yRQAXlABtQTXqNmeAHS9Ztow8dVhrw-fyOJaR87YycFZVUAXQBuIA)

```ts
"use strict";
letdirections = [
0/* Direction.Up */,
1/* Direction.Down */,
2/* Direction.Left */,
3/* Direction.Right */,
];
```

#### Const enum pitfalls {#const-enum-pitfalls}

Inlining enum values is straightforward at first, but comes with subtle implications.
These pitfalls pertain to *ambient* const enums only (basically const enums in `.d.ts` files) and sharing them between projects, but if you are publishing or consuming `.d.ts` files, these pitfalls likely apply to you, because `tsc --declaration` transforms `.ts` files into `.d.ts` files.

1. For the reasons laid out in the [`isolatedModules` documentation ↗](https://www.typescriptlang.org/tsconfig.html#references-to-const-enum-members), that mode is fundamentally incompatible with ambient const enums.
  This means if you publish ambient const enums, downstream consumers will not be able to use [`isolatedModules` ↗](https://www.typescriptlang.org/tsconfig.html#isolatedModules) and those enum values at the same time.
2. You can easily inline values from version A of a dependency at compile time, and import version B at runtime.
  Version A and B’s enums can have different values, if you are not very careful, resulting in [surprising bugs ↗](https://github.com/microsoft/TypeScript/issues/5219#issue-110947903), like taking the wrong branches of `if` statements.
  These bugs are especially pernicious because it is common to run automated tests at roughly the same time as projects are built, with the same dependency versions, which misses these bugs completely.
3. [`importsNotUsedAsValues: "preserve"` ↗](https://www.typescriptlang.org/tsconfig.html#importsNotUsedAsValues) will not elide imports for const enums used as values, but ambient const enums do not guarantee that runtime `.js` files exist.
  The unresolvable imports cause errors at runtime.
  The usual way to unambiguously elide imports, [type-only imports](/typescript/5.1/reference/modules#importing-types), [does not allow const enum values ↗](https://github.com/microsoft/TypeScript/issues/40344), currently.

Here are two approaches to avoiding these pitfalls:

1. Do not use const enums at all.
  You can easily [ban const enums ↗](https://typescript-eslint.io/linting/troubleshooting#how-can-i-ban-specific-language-feature) with the help of a linter.
  Obviously this avoids any issues with const enums, but prevents your project from inlining its own enums.
  Unlike inlining enums from other projects, inlining a project’s own enums is not problematic and has performance implications.
2. Do not publish ambient const enums, by deconstifying them with the help of [`preserveConstEnums` ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums).
  This is the approach taken internally by the [TypeScript project itself ↗](https://github.com/microsoft/TypeScript/pull/5422).
  [`preserveConstEnums` ↗](https://www.typescriptlang.org/tsconfig.html#preserveConstEnums) emits the same JavaScript for const enums as plain enums.
  You can then safely strip the `const` modifier from `.d.ts` files [in a build step ↗](https://github.com/microsoft/TypeScript/blob/1a981d1df1810c868a66b3828497f049a944951c/Gulpfile.js#L144).
  This way downstream consumers will not inline enums from your project, avoiding the pitfalls above, but a project can still inline its own enums, unlike banning const enums entirely.

## Ambient enums {#ambient-enums}

Ambient enums are used to describe the shape of already existing enum types.

[Try this code ↗](https://www.typescriptlang.org/play#code/CYUwxgNghgTiAEIB2BXAtvAoqjBvAUPPAILwC88AjADSHwBCtRAwufAEy0C+QA)

```ts
declare enum Enum {
A = 1,
B,
C = 2,
}
```

One important difference between ambient and non-ambient enums is that, in regular enums, members that don’t have an initializer will be considered constant if its preceding enum member is considered constant.
By contrast, an ambient (and non-const) enum member that does not have an initializer is *always* considered computed.

## Objects vs Enums {#objects-vs-enums}

In modern TypeScript, you may not need an enum when an object with `as const` could suffice:

[Try this code ↗](https://www.typescriptlang.org/play#code/MYewdgzgLgBApmArgWxgUQCIEsBOdhRbgwDeAUDDAKoAOANBTBiAO5gOUAycAZlBzABKWAOYALfmQC+ZMqEiwA8tjwEiYGAF5SjWgC4YABgHM2BgIwDufAwCYBw8VAMBmBlJgBDCDHnQA3LKYuPiE4AB0tIEA9NGU8QkAegD8ssohahFRZLEJeSmyuVQQWGAiMFBicPBIqN5eMDSeOJ7IcFBwOGQ8iGCZGiyeADYA1gAUACa4BsGqYWAAlKQyOXEAkrB4AI6IIT6eGnAAHlAtMEOl1VAgjYhDQzAgiLCV1QBuw4hwEGRQAJ40aoqULqLQVAFwEA8GDpObqADaIzgfyh4MBqNhIPAAF1Aj0+vMYDhepNpkwMvMliQVoNRmNZliwOFrFAFoFiWAxpj+uFHBI2UA)

```ts
const enum EDirection {
Up,
Down,
Left,
Right,
}

constODirection = {
Up:0,
Down:1,
Left:2,
Right:3,
} asconst;

EDirection.Up;

(enum member) EDirection.Up = 0

ODirection.Up;

(property) Up: 0

// Using the enum as a parameter
functionwalk(dir: EDirection) {}

// It requires an extra line to pull out the values
typeDirection = typeofODirection[keyoftypeofODirection];
functionrun(dir: Direction) {}

walk(EDirection.Left);
run(ODirection.Right);
```

The biggest argument in favour of this format over TypeScript’s `enum` is that it keeps your codebase aligned with the state of JavaScript, and [when/if ↗](https://github.com/rbuckton/proposal-enum) enums are added to JavaScript then you can move to the additional syntax.
