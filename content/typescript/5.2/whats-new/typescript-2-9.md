---
linkTitle: "TypeScript 2.9"
title: "TypeScript: Documentation - TypeScript 2.9"
description: "TypeScript 2.9 Release Notes"
weight: 24
type: docs
---

# TypeScript 2.9

## Support `number` and `symbol` named properties with `keyof` and mapped types {#support-number-and-symbol-named-properties-with-keyof-and-mapped-types}

TypeScript 2.9 adds support for `number` and `symbol` named properties in index types and mapped types.
Previously, the `keyof` operator and mapped types only supported `string` named properties.

Changes include:

- An index type `keyof T` for some type `T` is a subtype of `string | number | symbol`.
- A mapped type `{ [P in K]: XXX }` permits any `K` assignable to `string | number | symbol`.
- In a `for...in` statement for an object of a generic type `T`, the inferred type of the iteration variable was previously `keyof T` but is now `Extract<keyof T, string>`. (In other words, the subset of `keyof T` that includes only string-like values.)

Given an object type `X`, `keyof X` is resolved as follows:

- If `X` contains a string index signature, `keyof X` is a union of `string`, `number`, and the literal types representing symbol-like properties, otherwise
- If `X` contains a numeric index signature, `keyof X` is a union of `number` and the literal types representing string-like and symbol-like properties, otherwise
- `keyof X` is a union of the literal types representing string-like, number-like, and symbol-like properties.

Where:

- String-like properties of an object type are those declared using an identifier, a string literal, or a computed property name of a string literal type.
- Number-like properties of an object type are those declared using a numeric literal or computed property name of a numeric literal type.
- Symbol-like properties of an object type are those declared using a computed property name of a unique symbol type.

In a mapped type `{ [P in K]: XXX }`, each string literal type in `K` introduces a property with a string name, each numeric literal type in `K` introduces a property with a numeric name, and each unique symbol type in `K` introduces a property with a unique symbol name.
Furthermore, if `K` includes type `string`, a string index signature is introduced, and if `K` includes type `number`, a numeric index signature is introduced.

##### Example {#example}

```ts
const c = "c";
constd = 10;
conste = Symbol();

constenumE1 {
A,
B,
C,
}
constenumE2 {
A = "A",
B = "B",
C = "C",
}

typeFoo = {
a: string; // String-like name
5: string; // Number-like name
  [c]: string; // String-like name
  [d]: string; // Number-like name
  [e]: string; // Symbol-like name
  [E1.A]: string; // Number-like name
  [E2.A]: string; // String-like name
};

typeK1 = keyofFoo; // "a" | 5 | "c" | 10 | typeof e | E1.A | E2.A
typeK2 = Extract<keyofFoo, string>; // "a" | "c" | E2.A
typeK3 = Extract<keyofFoo, number>; // 5 | 10 | E1.A
typeK4 = Extract<keyofFoo, symbol>; // typeof e
```

Since `keyof` now reflects the presence of a numeric index signature by including type `number` in the key type, mapped types such as `Partial<T>` and `Readonly<T>` work correctly when applied to object types with numeric index signatures:

```ts
type Arrayish<T> = {
length: number;
  [x: number]: T;
};

typeReadonlyArrayish<T> = Readonly<Arrayish<T>>;

declareconstmap: ReadonlyArrayish<string>;
letn = map.length;
letx = map[123]; // Previously of type any (or an error with --noImplicitAny)
```

Furthermore, with the `keyof` operator’s support for `number` and `symbol` named keys, it is now possible to abstract over access to properties of objects that are indexed by numeric literals (such as numeric enum types) and unique symbols.

```ts
const enum Enum {
A,
B,
C,
}

constenumToStringMap = {
[Enum.A]:"Name A",
[Enum.B]:"Name B",
[Enum.C]:"Name C",
};

constsym1 = Symbol();
constsym2 = Symbol();
constsym3 = Symbol();

constsymbolToNumberMap = {
[sym1]:1,
[sym2]:2,
[sym3]:3,
};

typeKE = keyoftypeofenumToStringMap; // Enum (i.e. Enum.A | Enum.B | Enum.C)
typeKS = keyoftypeofsymbolToNumberMap; // typeof sym1 | typeof sym2 | typeof sym3

functiongetValue<T, KextendskeyofT>(obj: T, key: K): T[K] {
returnobj[key];
}

letx1 = getValue(enumToStringMap, Enum.C); // Returns "Name C"
letx2 = getValue(symbolToNumberMap, sym3); // Returns 3
```

This is a breaking change; previously, the `keyof` operator and mapped types only supported `string` named properties.
Code that assumed values typed with `keyof T` were always `string`s, will now be flagged as error.

##### Example {#example-1}

```ts
function useKey<T, K extends keyof T>(o: T, k: K) {
varname: string = k; // Error: keyof T is not assignable to string
}
```

#### Recommendations {#recommendations}

- If your functions are only able to handle string named property keys, use `Extract<keyof T, string>` in the declaration:

  ```ts
  function useKey<T, K extends Extract<keyof T, string>>(o: T, k: K) {
  varname: string = k; // OK
  }
  ```

- If your functions are open to handling all property keys, then the changes should be done down-stream:

  ```ts
  function useKey<T, K extends keyof T>(o: T, k: K) {
  varname: string | number | symbol = k;
  }
  ```

- Otherwise use [`keyofStringsOnly` ↗](https://www.typescriptlang.org/tsconfig.html#keyofStringsOnly) compiler option to disable the new behavior.

## Generic type arguments in JSX elements {#generic-type-arguments-in-jsx-elements}

JSX elements now allow passing type arguments to generic components.

##### Example {#example-2}

```ts
class GenericComponent<P> extends React.Component<P> {
internalProp: P;
}

typeProps = { a: number; b: string };

constx = <GenericComponent<Props> a={10} b="hi" />; // OK

consty = <GenericComponent<Props> a={10} b={20} />; // Error
```

## Generic type arguments in generic tagged templates {#generic-type-arguments-in-generic-tagged-templates}

Tagged templates are a form of invocation introduced in ECMAScript 2015.
Like call expressions, generic functions may be used in a tagged template and TypeScript will infer the type arguments utilized.

TypeScript 2.9 allows passing generic type arguments to tagged template strings.

##### Example {#example-3}

```ts
declare function styledComponent<Props>(
strs: TemplateStringsArray
): Component<Props>;

interfaceMyProps {
name: string;
age: number;
}

styledComponent<MyProps>`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

declarefunctiontag<T>(strs: TemplateStringsArray, ...args: T[]): T;

// inference fails because 'number' and 'string' are both candidates that conflict
leta = tag<string|number>`${100}${"hello"}`;
```

## `import` types {#import-types}

Modules can import types declared in other modules. But non-module global scripts cannot access types declared in modules. Enter `import` types.

Using `import("mod")` in a type annotation allows for reaching in a module and accessing its exported declaration without importing it.

##### Example {#example-4}

Given a declaration of a class `Pet` in a module file:

```ts
// module.d.ts

exportdeclareclassPet {
name: string;
}
```

Can be used in a non-module file `global-script.ts`:

```ts
// global-script.ts

functionadopt(p: import("./module").Pet) {
console.log(`Adopting ${p.name}...`);
}
```

This also works in JSDoc comments to refer to types from other modules in `.js`:

```js
// a.js

/**
 * @paramp { import("./module").Pet }
 */
functionwalk(p) {
console.log(`Walking ${p.name}...`);
}
```

## Relaxing declaration emit visibility rules {#relaxing-declaration-emit-visibility-rules}

With `import` types available, many of the visibility errors reported during declaration file generation can be handled by the compiler without the need to change the input.

For instance:

```ts
import { createHash } from "crypto";

exportconsthash = createHash("sha256");
//           ^^^^
// Exported variable 'hash' has or is using name 'Hash' from external module "crypto" but cannot be named.
```

With TypeScript 2.9, no errors are reported, and now the generated file looks like:

```ts
export declare const hash: import("crypto").Hash;
```

## Support for `import.meta` {#support-for-importmeta}

TypeScript 2.9 introduces support for `import.meta`, a new meta-property as described by the current [TC39 proposal ↗](https://github.com/tc39/proposal-import-meta).

The type of `import.meta` is the global `ImportMeta` type which is defined in `lib.es5.d.ts`.
This interface is extremely limited.
Adding well-known properties for Node or browsers requires interface merging and possibly a global augmentation depending on the context.

##### Example {#example-5}

Assuming that `__dirname` is always available on `import.meta`, the declaration would be done through reopening `ImportMeta` interface:

```ts
// node.d.ts
interfaceImportMeta {
__dirname: string;
}
```

And usage would be:

```ts
import.meta.__dirname; // Has type 'string'
```

`import.meta` is only allowed when targeting `ESNext` modules and ECMAScript targets.

## New `--resolveJsonModule` {#new---resolvejsonmodule}

Often in Node.js applications a `.json` is needed. With TypeScript 2.9, [`resolveJsonModule` ↗](https://www.typescriptlang.org/tsconfig.html#resolveJsonModule) allows for importing, extracting types from and generating `.json` files.

##### Example {#example-6}

```ts
// settings.json

{
"repo": "TypeScript",
"dry": false,
"debug": false
}
```

```ts
// a.ts

importsettingsfrom"./settings.json";

settings.debug === true; // OK
settings.dry === 2; // Error: Operator '===' cannot be applied boolean and number
```

```
// tsconfig.json

{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[module ↗](https://www.typescriptlang.org/tsconfig.html#module)": "commonjs",
"[resolveJsonModule ↗](https://www.typescriptlang.org/tsconfig.html#resolveJsonModule)": true,
"[esModuleInterop ↗](https://www.typescriptlang.org/tsconfig.html#esModuleInterop)": true
  }
}
```

## `--pretty` output by default {#--pretty-output-by-default}

Starting TypeScript 2.9 errors are displayed under [`pretty` ↗](https://www.typescriptlang.org/tsconfig.html#pretty) by default if the output device is applicable for colorful text.
TypeScript will check if the output stream has [`isTty` ↗](https://nodejs.org/api/tty.html) property set.

Use `--pretty false` on the command line or set `"pretty": false` in your `tsconfig.json` to disable [`pretty` ↗](https://www.typescriptlang.org/tsconfig.html#pretty) output.

## New `--declarationMap` {#new---declarationmap}

Enabling [`declarationMap` ↗](https://www.typescriptlang.org/tsconfig.html#declarationMap) alongside [`declaration` ↗](https://www.typescriptlang.org/tsconfig.html#declaration) causes the compiler to emit `.d.ts.map` files alongside the output `.d.ts` files.
Language Services can also now understand these map files, and uses them to map declaration-file based definition locations to their original source, when available.

In other words, hitting go-to-definition on a declaration from a `.d.ts` file generated with [`declarationMap` ↗](https://www.typescriptlang.org/tsconfig.html#declarationMap) will take you to the source file (`.ts`) location where that declaration was defined, and not to the `.d.ts`.
