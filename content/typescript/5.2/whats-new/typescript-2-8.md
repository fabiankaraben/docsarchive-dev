---
linkTitle: "TypeScript 2.8"
title: "TypeScript: Documentation - TypeScript 2.8"
description: "TypeScript 2.8 Release Notes"
weight: 25
type: docs
---

# TypeScript 2.8

## Conditional Types {#conditional-types}

TypeScript 2.8 introduces *conditional types* which add the ability to express non-uniform type mappings.
A conditional type selects one of two possible types based on a condition expressed as a type relationship test:

```ts
T extends U ? X : Y
```

The type above means when `T` is assignable to `U` the type is `X`, otherwise the type is `Y`.

A conditional type `T extends U ? X : Y` is either *resolved* to `X` or `Y`, or *deferred* because the condition depends on one or more type variables.
Whether to resolve or defer is determined as follows:

- First, given types `T'` and `U'` that are instantiations of `T` and `U` where all occurrences of type parameters are replaced with `any`, if `T'` is not assignable to `U'`, the conditional type is resolved to `Y`. Intuitively, if the most permissive instantiation of `T` is not assignable to the most permissive instantiation of `U`, we know that no instantiation will be and we can just resolve to `Y`.
- Next, for each type variable introduced by an `infer` (more later) declaration within `U` collect a set of candidate types by inferring from `T` to `U` (using the same inference algorithm as type inference for generic functions). For a given `infer` type variable `V`, if any candidates were inferred from co-variant positions, the type inferred for `V` is a union of those candidates. Otherwise, if any candidates were inferred from contra-variant positions, the type inferred for `V` is an intersection of those candidates. Otherwise, the type inferred for `V` is `never`.
- Then, given a type `T''` that is an instantiation of `T` where all `infer` type variables are replaced with the types inferred in the previous step, if `T''` is *definitely assignable* to `U`, the conditional type is resolved to `X`. The definitely assignable relation is the same as the regular assignable relation, except that type variable constraints are not considered. Intuitively, when a type is definitely assignable to another type, we know that it will be assignable for *all instantiations* of those types.
- Otherwise, the condition depends on one or more type variables and the conditional type is deferred.

##### Example {#example}

```ts
type TypeName<T> = T extends string
  ? "string"
  : Textendsnumber
  ? "number"
  : Textendsboolean
  ? "boolean"
  : Textendsundefined
  ? "undefined"
  : TextendsFunction
  ? "function"
  : "object";

typeT0 = TypeName<string>; // "string"
typeT1 = TypeName<"a">; // "string"
typeT2 = TypeName<true>; // "boolean"
typeT3 = TypeName<() =>void>; // "function"
typeT4 = TypeName<string[]>; // "object"
```

## Distributive conditional types {#distributive-conditional-types}

Conditional types in which the checked type is a naked type parameter are called *distributive conditional types*.
Distributive conditional types are automatically distributed over union types during instantiation.
For example, an instantiation of `T extends U ? X : Y` with the type argument `A | B | C` for `T` is resolved as `(A extends U ? X : Y) | (B extends U ? X : Y) | (C extends U ? X : Y)`.

##### Example {#example-1}

```ts
type T10 = TypeName<string | (() => void)>; // "string" | "function"
typeT12 = TypeName<string | string[] | undefined>; // "string" | "object" | "undefined"
typeT11 = TypeName<string[] | number[]>; // "object"
```

In instantiations of a distributive conditional type `T extends U ? X : Y`, references to `T` within the conditional type are resolved to individual constituents of the union type (i.e. `T` refers to the individual constituents *after* the conditional type is distributed over the union type).
Furthermore, references to `T` within `X` have an additional type parameter constraint `U` (i.e. `T` is considered assignable to `U` within `X`).

##### Example {#example-2}

```ts
type BoxedValue<T> = { value: T };
typeBoxedArray<T> = { array: T[] };
typeBoxed<T> = Textendsany[] ? BoxedArray<T[number]> : BoxedValue<T>;

typeT20 = Boxed<string>; // BoxedValue<string>;
typeT21 = Boxed<number[]>; // BoxedArray<number>;
typeT22 = Boxed<string | number[]>; // BoxedValue<string> | BoxedArray<number>;
```

Notice that `T` has the additional constraint `any[]` within the true branch of `Boxed<T>` and it is therefore possible to refer to the element type of the array as `T[number]`. Also, notice how the conditional type is distributed over the union type in the last example.

The distributive property of conditional types can conveniently be used to *filter* union types:

```ts
type Diff<T, U> = T extends U ? never : T; // Remove types from T that are assignable to U
typeFilter<T, U> = TextendsU ? T : never; // Remove types from T that are not assignable to U

typeT30 = Diff<"a" | "b" | "c" | "d", "a" | "c" | "f">; // "b" | "d"
typeT31 = Filter<"a" | "b" | "c" | "d", "a" | "c" | "f">; // "a" | "c"
typeT32 = Diff<string | number | (() =>void), Function>; // string | number
typeT33 = Filter<string | number | (() =>void), Function>; // () => void

typeNonNullable<T> = Diff<T, null | undefined>; // Remove null and undefined from T

typeT34 = NonNullable<string | number | undefined>; // string | number
typeT35 = NonNullable<string | string[] | null | undefined>; // string | string[]

functionf1<T>(x: T, y: NonNullable<T>) {
x = y; // Ok
y = x; // Error
}

functionf2<Textendsstring | undefined>(x: T, y: NonNullable<T>) {
x = y; // Ok
y = x; // Error
lets1: string = x; // Error
lets2: string = y; // Ok
}
```

Conditional types are particularly useful when combined with mapped types:

```ts
type FunctionPropertyNames<T> = {
  [KinkeyofT]: T[K] extendsFunction ? K : never;
}[keyofT];
typeFunctionProperties<T> = Pick<T, FunctionPropertyNames<T>>;

typeNonFunctionPropertyNames<T> = {
  [KinkeyofT]: T[K] extendsFunction ? never : K;
}[keyofT];
typeNonFunctionProperties<T> = Pick<T, NonFunctionPropertyNames<T>>;

interfacePart {
id: number;
name: string;
subparts: Part[];
updatePart(newName: string): void;
}

typeT40 = FunctionPropertyNames<Part>; // "updatePart"
typeT41 = NonFunctionPropertyNames<Part>; // "id" | "name" | "subparts"
typeT42 = FunctionProperties<Part>; // { updatePart(newName: string): void }
typeT43 = NonFunctionProperties<Part>; // { id: number, name: string, subparts: Part[] }
```

Similar to union and intersection types, conditional types are not permitted to reference themselves recursively.
For example the following is an error.

##### Example {#example-3}

```ts
type ElementType<T> = T extends any[] ? ElementType<T[number]> : T; // Error
```

## Type inference in conditional types {#type-inference-in-conditional-types}

Within the `extends` clause of a conditional type, it is now possible to have `infer` declarations that introduce a type variable to be inferred.
Such inferred type variables may be referenced in the true branch of the conditional type.
It is possible to have multiple `infer` locations for the same type variable.

For example, the following extracts the return type of a function type:

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

Conditional types can be nested to form a sequence of pattern matches that are evaluated in order:

```ts
type Unpacked<T> = T extends (infer U)[]
  ? U
  : Textends (...args: any[]) =>inferU
  ? U
  : TextendsPromise<inferU>
  ? U
  : T;

typeT0 = Unpacked<string>; // string
typeT1 = Unpacked<string[]>; // string
typeT2 = Unpacked<() =>string>; // string
typeT3 = Unpacked<Promise<string>>; // string
typeT4 = Unpacked<Promise<string>[]>; // Promise<string>
typeT5 = Unpacked<Unpacked<Promise<string>[]>>; // string
```

The following example demonstrates how multiple candidates for the same type variable in co-variant positions causes a union type to be inferred:

```ts
type Foo<T> = T extends { a: infer U; b: infer U } ? U : never;
typeT10 = Foo<{ a: string; b: string }>; // string
typeT11 = Foo<{ a: string; b: number }>; // string | number
```

Likewise, multiple candidates for the same type variable in contra-variant positions causes an intersection type to be inferred:

```ts
type Bar<T> = T extends { a: (x: infer U) => void; b: (x: infer U) => void }
  ? U
  : never;
typeT20 = Bar<{ a: (x: string) =>void; b: (x: string) =>void }>; // string
typeT21 = Bar<{ a: (x: string) =>void; b: (x: number) =>void }>; // string & number
```

When inferring from a type with multiple call signatures (such as the type of an overloaded function), inferences are made from the *last* signature (which, presumably, is the most permissive catch-all case).
It is not possible to perform overload resolution based on a list of argument types.

```ts
declare function foo(x: string): number;
declarefunctionfoo(x: number): string;
declarefunctionfoo(x: string | number): string | number;
typeT30 = ReturnType<typeoffoo>; // string | number
```

It is not possible to use `infer` declarations in constraint clauses for regular type parameters:

```ts
type ReturnType<T extends (...args: any[]) => infer R> = R; // Error, not supported
```

However, much the same effect can be obtained by erasing the type variables in the constraint and instead specifying a conditional type:

```ts
type AnyFunction = (...args: any[]) => any;
typeReturnType<TextendsAnyFunction> = Textends (...args: any[]) =>inferR
  ? R
  : any;
```

## Predefined conditional types {#predefined-conditional-types}

TypeScript 2.8 adds several predefined conditional types to `lib.d.ts`:

- `Exclude<T, U>` — Exclude from `T` those types that are assignable to `U`.
- `Extract<T, U>` — Extract from `T` those types that are assignable to `U`.
- `NonNullable<T>` — Exclude `null` and `undefined` from `T`.
- `ReturnType<T>` — Obtain the return type of a function type.
- `InstanceType<T>` — Obtain the instance type of a constructor function type.

##### Example {#example-4}

```ts
type T00 = Exclude<"a" | "b" | "c" | "d", "a" | "c" | "f">; // "b" | "d"
typeT01 = Extract<"a" | "b" | "c" | "d", "a" | "c" | "f">; // "a" | "c"

typeT02 = Exclude<string | number | (() =>void), Function>; // string | number
typeT03 = Extract<string | number | (() =>void), Function>; // () => void

typeT04 = NonNullable<string | number | undefined>; // string | number
typeT05 = NonNullable<(() =>string) | string[] | null | undefined>; // (() => string) | string[]

functionf1(s: string) {
return { a:1, b:s };
}

classC {
x = 0;
y = 0;
}

typeT10 = ReturnType<() =>string>; // string
typeT11 = ReturnType<(s: string) =>void>; // void
typeT12 = ReturnType<<T>() =>T>; // {}
typeT13 = ReturnType<<TextendsU, Uextendsnumber[]>() =>T>; // number[]
typeT14 = ReturnType<typeoff1>; // { a: number, b: string }
typeT15 = ReturnType<any>; // any
typeT16 = ReturnType<never>; // any
typeT17 = ReturnType<string>; // Error
typeT18 = ReturnType<Function>; // Error

typeT20 = InstanceType<typeofC>; // C
typeT21 = InstanceType<any>; // any
typeT22 = InstanceType<never>; // any
typeT23 = InstanceType<string>; // Error
typeT24 = InstanceType<Function>; // Error
```

> Note: The `Exclude` type is a proper implementation of the `Diff` type suggested [here ↗](https://github.com/Microsoft/TypeScript/issues/12215#issuecomment-307871458). We’ve used the name `Exclude` to avoid breaking existing code that defines a `Diff`, plus we feel that name better conveys the semantics of the type. We did not include the `Omit<T, K>` type because it is trivially written as `Pick<T, Exclude<keyof T, K>>`.
> 

## Improved control over mapped type modifiers {#improved-control-over-mapped-type-modifiers}

Mapped types support adding a `readonly` or `?` modifier to a mapped property, but they did not provide support the ability to *remove* modifiers.
This matters in [*homomorphic mapped types* ↗](https://github.com/Microsoft/TypeScript/pull/12563) which by default preserve the modifiers of the underlying type.

TypeScript 2.8 adds the ability for a mapped type to either add or remove a particular modifier.
Specifically, a `readonly` or `?` property modifier in a mapped type can now be prefixed with either `+` or `-` to indicate that the modifier should be added or removed.

#### Example {#example-5}

```ts
type MutableRequired<T> = { -readonly [P in keyof T]-?: T[P] }; // Remove readonly and ?
typeReadonlyPartial<T> = { +readonly [PinkeyofT]+?: T[P] }; // Add readonly and ?
```

A modifier with no `+` or `-` prefix is the same as a modifier with a `+` prefix. So, the `ReadonlyPartial<T>` type above corresponds to

```ts
type ReadonlyPartial<T> = { readonly [P in keyof T]?: T[P] }; // Add readonly and ?
```

Using this ability, `lib.d.ts` now has a new `Required<T>` type.
This type strips `?` modifiers from all properties of `T`, thus making all properties required.

##### Example {#example-6}

```ts
type Required<T> = { [P in keyof T]-?: T[P] };
```

Note that in [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) mode, when a homomorphic mapped type removes a `?` modifier from a property in the underlying type it also removes `undefined` from the type of that property:

##### Example {#example-7}

```ts
type Foo = { a?: string }; // Same as { a?: string | undefined }
typeBar = Required<Foo>; // Same as { a: string }
```

## Improved `keyof` with intersection types {#improved-keyof-with-intersection-types}

With TypeScript 2.8 `keyof` applied to an intersection type is transformed to a union of `keyof` applied to each intersection constituent.
In other words, types of the form `keyof (A & B)` are transformed to be `keyof A | keyof B`.
This change should address inconsistencies with inference from `keyof` expressions.

##### Example {#example-8}

```ts
type A = { a: string };
typeB = { b: string };

typeT1 = keyof (A & B); // "a" | "b"
typeT2<T> = keyof (T & B); // keyof T | "b"
typeT3<U> = keyof (A & U); // "a" | keyof U
typeT4<T, U> = keyof (T & U); // keyof T | keyof U
typeT5 = T2<A>; // "a" | "b"
typeT6 = T3<B>; // "a" | "b"
typeT7 = T4<A, B>; // "a" | "b"
```

## Better handling for namespace patterns in `.js` files {#better-handling-for-namespace-patterns-in-js-files}

TypeScript 2.8 adds support for understanding more namespace patterns in `.js` files.
Empty object literals declarations on top level, just like functions and classes, are now recognized as as namespace declarations in JavaScript.

```js
var ns = {}; // recognized as a declaration for a namespace `ns`
ns.constant = 1; // recognized as a declaration for var `constant`
```

Assignments at the top-level should behave the same way; in other words, a `var` or `const` declaration is not required.

```js
app = {}; // does NOT need to be `var app = {}`
app.C = class {};
app.f = function() {};
app.prop = 1;
```

## IIFEs as namespace declarations {#iifes-as-namespace-declarations}

An IIFE returning a function, class or empty object literal, is also recognized as a namespace:

```js
var C = (function() {
functionC(n) {
this.p = n;
  }
returnC;
})();
C.staticProperty = 1;
```

## Defaulted declarations {#defaulted-declarations}

“Defaulted declarations” allow initializers that reference the declared name in the left side of a logical or:

```js
my = window.my || {};
my.app = my.app || {};
```

## Prototype assignment {#prototype-assignment}

You can assign an object literal directly to the prototype property. Individual prototype assignments still work too:

```ts
var C = function(p) {
this.p = p;
};
C.prototype = {
m() {
console.log(this.p);
  }
};
C.prototype.q = function(r) {
returnthis.p === r;
};
```

## Nested and merged declarations {#nested-and-merged-declarations}

Nesting works to any level now, and merges correctly across files. Previously neither was the case.

```js
var app = window.app || {};
app.C = class {};
```

## Per-file JSX factories {#per-file-jsx-factories}

TypeScript 2.8 adds support for a per-file configurable JSX factory name using `@jsx dom` pragma.
JSX factory can be configured for a compilation using [`jsxFactory` ↗](https://www.typescriptlang.org/tsconfig.html#jsxFactory) (default is `React.createElement`). With TypeScript 2.8 you can override this on a per-file-basis by adding a comment to the beginning of the file.

##### Example {#example-9}

```ts
/** @jsx dom */
import { dom } from"./renderer";
<h></h>;
```

Generates:

```js
var renderer_1 = require("./renderer");
renderer_1.dom("h", null);
```

## Locally scoped JSX namespaces {#locally-scoped-jsx-namespaces}

JSX type checking is driven by definitions in a JSX namespace, for instance `JSX.Element` for the type of a JSX element, and `JSX.IntrinsicElements` for built-in elements.
Before TypeScript 2.8 the `JSX` namespace was expected to be in the global namespace, and thus only allowing one to be defined in a project.
Starting with TypeScript 2.8 the `JSX` namespace will be looked under the `jsxNamespace` (e.g. `React`) allowing for multiple jsx factories in one compilation.
For backward compatibility the global `JSX` namespace is used as a fallback if none was defined on the factory function.
Combined with the per-file `@jsx` pragma, each file can have a different JSX factory.

## New `--emitDeclarationOnly` {#new---emitdeclarationonly}

[`emitDeclarationOnly` ↗](https://www.typescriptlang.org/tsconfig.html#emitDeclarationOnly) allows for *only* generating declaration files; `.js`/`.jsx` output generation will be skipped with this flag. The flag is useful when the `.js` output generation is handled by a different transpiler like Babel.
