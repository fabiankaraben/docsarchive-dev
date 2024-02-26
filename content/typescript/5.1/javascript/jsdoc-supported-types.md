---
linkTitle: "JSDoc Reference"
title: "TypeScript: Documentation - JSDoc Reference"
description: "What JSDoc does TypeScript-powered JavaScript support?"
weight: 3
type: docs
---

# JSDoc Reference

The list below outlines which constructs are currently supported
when using JSDoc annotations to provide type information in JavaScript files.

Note any tags which are not explicitly listed below (such as `@async`) are not yet supported.

#### Types {#types}

- [`@type`](/typescript/5.1/javascript/jsdoc-supported-types#type)
- [`@param`](/typescript/5.1/javascript/jsdoc-supported-types#param-and-returns) (or [`@arg`](/typescript/5.1/javascript/jsdoc-supported-types#param-and-returns) or [`@argument`](/typescript/5.1/javascript/jsdoc-supported-types#param-and-returns))
- [`@returns`](/typescript/5.1/javascript/jsdoc-supported-types#param-and-returns) (or [`@return`](/typescript/5.1/javascript/jsdoc-supported-types#param-and-returns))
- [`@typedef`](/typescript/5.1/javascript/jsdoc-supported-types#typedef-callback-and-param)
- [`@callback`](/typescript/5.1/javascript/jsdoc-supported-types#typedef-callback-and-param)
- [`@template`](/typescript/5.1/javascript/jsdoc-supported-types#template)
- [`@satisfies`](/typescript/5.1/javascript/jsdoc-supported-types#satisfies)

#### Classes {#classes}

- [Property Modifiers](/typescript/5.1/javascript/jsdoc-supported-types#property-modifiers)`@public`, `@private`, `@protected`, `@readonly`
- [`@override`](/typescript/5.1/javascript/jsdoc-supported-types#override)
- [`@extends`](/typescript/5.1/javascript/jsdoc-supported-types#extends) (or [`@augments`](/typescript/5.1/javascript/jsdoc-supported-types#extends))
- [`@implements`](/typescript/5.1/javascript/jsdoc-supported-types#implements)
- [`@class`](/typescript/5.1/javascript/jsdoc-supported-types#constructor) (or [`@constructor`](/typescript/5.1/javascript/jsdoc-supported-types#constructor))
- [`@this`](/typescript/5.1/javascript/jsdoc-supported-types#this)

#### Documentation {#documentation}

Documentation tags work in both TypeScript and JavaScript.

- [`@deprecated`](/typescript/5.1/javascript/jsdoc-supported-types#deprecated)
- [`@see`](/typescript/5.1/javascript/jsdoc-supported-types#see)
- [`@link`](/typescript/5.1/javascript/jsdoc-supported-types#link)

#### Other {#other}

- [`@enum`](/typescript/5.1/javascript/jsdoc-supported-types#enum)
- [`@author`](/typescript/5.1/javascript/jsdoc-supported-types#author)
- [Other supported patterns](/typescript/5.1/javascript/jsdoc-supported-types#other-supported-patterns)
- [Unsupported patterns](/typescript/5.1/javascript/jsdoc-supported-types#unsupported-patterns)
- [Unsupported tags](/typescript/5.1/javascript/jsdoc-supported-types#unsupported-tags)

The meaning is usually the same, or a superset, of the meaning of the tag given at [jsdoc.app ↗](https://jsdoc.app/).
The code below describes the differences and gives some example usage of each tag.

**Note:** You can use [the playground to explore JSDoc support ↗](https://www.typescriptlang.org/play#example/jsdoc-support).

## Types {#types-1}

### `@type` {#type}

You can reference types with the “@type” tag. The type can be:

1. Primitive, like `string` or `number`.
2. Declared in a TypeScript declaration, either global or imported.
3. Declared in a JSDoc [`@typedef`](/typescript/5.1/javascript/jsdoc-supported-types#typedef-callback-and-param) tag.

You can use most JSDoc type syntax and any TypeScript syntax, from [the most basic like `string`](/typescript/5.1/handbook/basic-types) to [the most advanced, like conditional types](/typescript/5.1/handbook/type-manipulation/conditional-types).

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4Gd4CcCWAdgOYC+UIw4AbgIa6RYDc44oMCK6GA6kQCYB7AO6loVOg2FEWbMHCRpMABVyCAtviyoAMvgDWqADw4CJAHxjKNepGRrN2-gGU8RYrODBIATUEBXSABjWkJGNCD8ADNESFDIAAkAFQBZHUgAUQAbVHVUQnhIaXgAC0gAEQB5FLs1NFx4fFQsOQ5FbmS07Nz8+CsJW3VEbryCyABeSCEg-1H4ADoAR39UXERnVByg+EFcAAptLZ3cAEoWTZ6C+f5aeFptBaHy29oJyAAid6YgA)

```js
/**
 * @type{string}
 */
vars;

/** @type{Window} */
varwin;

/** @type{PromiseLike<string>} */
varpromisedString;

// You can specify an HTML Element with DOM properties
/** @type{HTMLElement} */
varmyElement = document.querySelector(selector);
element.dataset.myData = "";
```

`@type` can specify a union type — for example, something can be either a string or a boolean.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4Gd4CcCWAdgOaQA+kARgPbUA2qAhoQL5QjDgBujukWlANxA)

```js
/**
 * @type{string | boolean}
 */
varsb;
```

You can specify array types using a variety of syntaxes:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCm4DeA7ArgWwEaIE4DaAugL7gjABQAbgIb7hoDOA3FaBDAsigIL58dWADoAPJlwEAfOUq0G4AFbMAJgHsAxu05Q4SVAKGwJ2PPlkVq9RmjpsgA)

```js
/** @type {number[]} */
varns;
/** @type{Array.<number>} */
varjsdoc;
/** @type{Array<number>} */
varnas;
```

You can also specify object literal types.
For example, an object with properties ‘a’ (string) and ‘b’ (number) uses the following syntax:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCm4DeLwEMBc4DO0AnASwDsBzAGnACMcSBXAW2sQPAF93wRgAoANwxtBBAJwBuIA)

```js
/** @type {{ a: string, b: number }} */
varvar9;
```

You can specify map-like and array-like objects using string and number index signatures, using either standard JSDoc syntax or TypeScript syntax.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUhBSC2BDADgWgDYEsDWBTSAewCMArPAYwBdIqALJG5FAZ0iQCdisqPOBPSAAMWvLADsA5kMgoOhFHg5UseNlULDxAVwTElIgHRQI0SAAEq-RZADeAeTKUqhgDyiOEyQBpIOvUoAfAC+JsDgAG6ckB5eACqEAHK6+hwA3ODgoDCW1gQOTtRu-qm+JOTUIdDhURzsHHz8ADK4eGlAA)

```js
/**
 * A map-like object that maps arbitrary `string` properties to `number`s.
 *
 * @type{Object.<string, number>}
 */
varstringToNumber;

/** @type{Object.<number, object>} */
vararrayLike;
```

The preceding two types are equivalent to the TypeScript types `{ [x: string]: number }` and `{ [x: number]: any }`. The compiler understands both syntaxes.

You can specify function types using either TypeScript or Google Closure syntax:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCm4DeAzArgOwMbQJYD2WAFAM7QBO+WA5gDTgBGhhANogIZYCUAXOCwYAtk0SUAvuADCbQmQyVkZWFmicAHuBDAAUADdOlcGSZYA3LtAQYCZCnICK1OoyYCW7Lr3ABeAHyCImKS4AAqdgDKONTw0Caq6lo6BkYmZgBM5kA)

```js
/** @type {function(string, boolean): number} Closure syntax */
varsbn;
/** @type{(s: string, b: boolean) => number} TypeScript syntax */
varsbn2;
```

Or you can just use the unspecified `Function` type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCm4DeAxArgOwMbQJYD2WAvuCMAFABuAhgE7gBmWA7ANyWgQwLIpNseIqXJU6jFgDZ2QA)

```js
/** @type {Function} */
varfn7;
/** @type{function} */
varfn6;
```

Other types from Closure also work:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4gL6QLSQDGAhgHaQBG6A5GYtZEmlCMOAG7EBOkAzvNwDc4UBGhwm6DAH5cBAK6kA1qQD2Ad3KTIACl7EAtumK9ItUvQCULNpx4BHean4BLVaUFA)

```js
/**
 * @type{*} - can be 'any' type
 */
varstar;
/**
 * @type{?} - unknown type (same as 'any')
 */
varquestion;
```

#### Casts {#casts}

TypeScript borrows cast syntax from Google Closure.
This lets you cast types to other types by adding a `@type` tag before any parenthesized expression.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4DsCuBbAI1QCdIAfSAZ3mIEssBzAXyhGHADcBDU3QkgPLEAyjXoNIAXkgBZLvAAWAOmJcsAEwD2eABQBKSAB5IABiUBWSAH5IAIgWoANo823IALkgBGEyYDcnDyQSGgAgpSUJPCo6gBy+ESk0qAwCCjo2AkkTNDAkDp8iUKidIx6fkA)

```js
/**
 * @type{number | string}
 */
varnumberOrString = Math.random() < 0.5 ? "hello" : 100;
vartypeAssertedNumber = /** @type{number} */ (numberOrString);
```

You can even cast to `const` just like TypeScript:

[Try this code ↗](https://www.typescriptlang.org/play#code/DYUwLgBA9gdiEF4IHoBUqIAEwE8AO8A3gMawDOYAvhKsgBQCMAlANxA)

```js
let one = /** @type {const} */(1);
```

#### Import types {#import-types}

You can import declarations from other files using import types.
This syntax is TypeScript-specific and differs from the JSDoc standard:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDMEsBsFMB2BDAtvAXKALgTwA7wDOAdACYnZEBQ8AHvgPYBO2OB8oACvGwLygA3tVCgU6LEWzNoiAOYAaagF8A3NWogIMBOMyhUyWSQBWNYACoLIixHzJmaIdFRNWACgBEJYHkJFPAEoSHmxlUHwbYGpIAFdEAGNsaEZEUAB3ZFgAa3d8QKERUATUokYEElhGOXcAAwB1LOzZOVAAEkF8Ej1lEj7awPVlIA)

```js
// @filename: types.d.ts
exporttypePet = {
name: string,
};

// @filename: main.js
/**
 * @param{import("./types").Pet}p
 */
functionwalk(p) {
console.log(`Walking ${p.name}...`);
}
```

import types can be used in type alias declarations:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDMEsBsFMB2BDAtvAXKALgTwA7wDOAdACYnZEBQ8AHvgPYBO2OB8oACvGwLygA3tVCgU6LEWzNoiAOYAaagF8A3NRAQYCcZlCpkskgCsamgLSWAxgFdsl8xoBUTkU4h5CZeJCHRUTKwAFABEJMCexCEAlCQ82MrcvG7A1M6uoO7gkULxyinUAG7IzPq48eqo5bwkuqpAA)

```js
/**
 * @typedef{import("./types").Pet}Pet
 */

/**
 * @type{Pet}
 */
varmyPet;
myPet.name;
```

import types can be used to get the type of a value from a module if you don’t know the type, or if it has a large type that is annoying to type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDMEsBsFMB2BDAtvAXKZBjHB7AV0QBcBnAOgBMLyAoeADwAd8AnE0AxMzws+GwCCeIqVABeUAG86oUCnRYARADk08ZQBo52KlTbwyZFUMR6DRstt2teyWAGF8VTKGU35BYiTYBPFU9QZlhkRHgSQJ15Mj9eeFQo3QBzB2RGAPcg4mgAN0EBJIBfAG46EAgYBEU3VGRoRAoAKzJysABaTpxCEk728oAqAbkBiBI-ZngZccn8SFBoVFYOAAplCmBcb1JrAEoKfkERbZIikeA6XOQ2UEZJUEMAR0JoQzWNrbFyZX3D4VEfCUgA)

```js
/**
 * @type{typeof import("./accounts").userAccount}
 */
varx = require("./accounts").userAccount;
```

### `@param` and `@returns` {#param-and-returns}

`@param` uses the same type syntax as `@type`, but adds a parameter name.
The parameter may also be declared optional by surrounding the name with square brackets:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAUEMCdIWwKYBcHQM6jpAnqARgqACYIDGANjAkaAJYB2okoAbjLcrgPYBmoa2ekkikktUqB5docNAChgAKkVzQi0AAEADjHigA3miTQGAcwC+oUFoCMoALSgAgv2NnruuADpV67Z4MjE3pTAF5LLQAmB2dGLi0xLnpIcg9YOFAACgBxLi5TckIAYXIuNABXaEIBIUgADwBKX00ddMC3EMsAbS0AZgBdGKd6LiQAC1RQeMTk1Na9TIApAGUAES4JGuFGnzUWgMMOi1AegBZQgCIUIwvBx2GphNoklLS9AHdacaZiBB5IcrkJCsFLlBDNDRVJCVegYQ7BY4AFTGtAwqNA40IVQqQN8wDkPHK9FEz0YQTMaGWRypCLQmVsABprJEmX1WacGgZVKAQKBEQB5Vb8uTmIA)

```js
// Parameters may be declared in a variety of syntactic forms
/**
 * @param{string}p1 - A string param.
 * @param{string=}p2 - An optional param (Google Closure syntax)
 * @param{string}[p3] - Another optional param (JSDoc syntax).
 * @param{string}[p4="test"] - An optional param with a default value
 * @returns{string} This is the result
 */
functionstringsStringStrings(p1, p2, p3, p4) {
// TODO
}
```

Likewise, for the return type of a function:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAnApgFwK7wHaQN4AV4D2AtgJYDOiAMiQNaIA8Zy8JGA5gHwC+UIw4AM1QYAxshIEsABzIAKAJQ4e4UBGhwkaTGRzZIAQwBckJi3YAaSACNjGVESuJ4kLl0gBaSAFl9AT0ioFJAA5Ago6BhkwQY6AO6IADYJMSFhWhjBvPxCouKSBlYKSkA)

```js
/**
 * @return{PromiseLike<string>}
 */
functionps() {}

/**
 * @returns{{ a: string, b: number }} - May use '@returns' as well as '@return'
 */
functionab() {}
```

### `@typedef`, `@callback`, and `@param` {#typedef-callback-and-param}

You can define complex types with `@typedef`.
Similar syntax works with `@param`.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKYBNUDNIG8DyARgFaoDG8AvpAMppkCWAhgDYAqKqkAtJGQE6om8VAGdITSADtUAd0hI00pgFsMkAOR1yzdpw1QYsZPwD2afkjyj4-BlIDm1E+YCMPCZBt3HkFxatTHG1GVg40Qzh-VEtEPCkAVxVCGOczZAAmD0lE5Ji-dJjA4PpdcNRI40LY+KSU-gBeNPMAZmypSHN4BlMpVmk6-OiaoNpSsM5K-1q8-moAbX8AFgBdds7kbt7+3PqC8yK40ZCyyego9Jn6hf8AVgaljLXeJg6unr6WAdn9gKOSnQTJSyBjwAAWkEwWCYCRY8EMwHA4FARkUXFwJyBqGoIERADcmPwvOM9GgiKQKABucCiEnlcnkeAAOn8LUpQA)

```js
/**
 * @typedef{Object}SpecialType - creates a new type named 'SpecialType'
 * @property{string}prop1 - a string property of SpecialType
 * @property{number}prop2 - a number property of SpecialType
 * @property{number=}prop3 - an optional number property of SpecialType
 * @prop{number}[prop4] - an optional number property of SpecialType
 * @prop{number}[prop5=42] - an optional number property of SpecialType with default
 */

/** @type{SpecialType} */
varspecialTypeObject;
specialTypeObject.prop3;
```

You can use either `object` or `Object` on the first line.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKYBNUDNIG8D2ARgFaoDG8AvpAMppkCWAhgDYAqKqAjJALSRkATqibxUAZ0hNIAO1QB3SEjSymAWwyQA5HXLN2nLlqgxYyQfjSCkecfEEMZAc2rnLPftLsPnkN1Zt8HF1GVg40LhM4f1RrRDwZAFc1QljXC2QAJj4pWWTUwT8M2MDg+n1w7iizYriE-NiAXnTLAGYcphlIS3gGfBlWPJTYossS+KDacrDDE2BwcFBTZVQ8EIrDahB5gDcmQvFpgzQAeRJyeC4AbiA)

```js
/**
 * @typedef{object}SpecialType1 - creates a new type named 'SpecialType1'
 * @property{string}prop1 - a string property of SpecialType1
 * @property{number}prop2 - a number property of SpecialType1
 * @property{number=}prop3 - an optional number property of SpecialType1
 */

/** @type{SpecialType1} */
varspecialTypeObject1;
```

`@param` allows a similar syntax for one-off type specifications.
Note that the nested property names must be prefixed with the name of the parameter:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAHAhgJ0QW0gbwPICMBWApgMYAuAvpAPbykCWVAdgM6QC0kAKgBaGTPdE8PnVale-dH0SsAysOJ1EAG04BPYZES4qAN0JQYCFOizNSyOowDmlGvSbMAdPGQ0AjIbhJUGTIwBXNFxCZDtaBhYXN3gAJi9jXyxA4NCAXnCHKNcaAGYEn1N-IJCwyABte0jnHPgAFgBdApM-FNLKSojHaJoAVjS62KboYHAAMwDGMkj+BSVlAAoqxwBKLChIZEJSAORGSCWu7Ji6yAAfM8h3AAZr9zWAamojmpjegG5wciA)

```js
/**
 * @param{Object}options - The shape is the same as SpecialType above
 * @param{string}options.prop1
 * @param{number}options.prop2
 * @param{number=}options.prop3
 * @param{number}[options.prop4]
 * @param{number}[options.prop5=42]
 */
functionspecial(options) {
return (options.prop4 || 1001) + options.prop5;
}
```

`@callback` is similar to `@typedef`, but it specifies a function type instead of an object type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBBjAhgG2QI0fA1pACgJwFMATASyQBdCoZYAHRfRAW0gG8BnC-UgOwHMAvpGKIKiGnAZNWbXgFdmaQvmEBtPsUIAPALqTYRCvPy8O7NAHtLyQol6CawcOFC0KATzqF2BEuTFCYRBneEszCkhLHABeSAAKDgBKSBiAPkgAQkSAOlsBCgALSABSSAAmJIBuIA)

```js
/**
 * @callbackPredicate
 * @param{string}data
 * @param{number}[index]
 * @returns{boolean}
 */

/** @type{Predicate} */
constok = (s) => !(s.length % 2);
```

Of course, any of these types can be declared using TypeScript syntax in a single-line `@typedef`:

```js
/** @typedef {{ prop1: string, prop2: string, prop3?: number }} SpecialType */
/** @typedef{(data: string, index?: number) => boolean}Predicate */
```

### `@template` {#template}

You can declare type parameters with the `@template` tag.
This lets you make functions, classes, or types that are generic:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXApgWwA4BsCGTIBUoyyqYBOmykA3rgL6QAekAtJAIKQDmiAdoiQJYBjSMTLJESEpHgALbJABm6APYB3AM7SZJZQFcOM6cq2JIJCbpLdpAT1SICcc-EvdN1GgWDgFu7oPh+ZWt+ABMACnoASiooMwsrBgBucE9wQWD1eEhMSABeSDDwgCIsgW4OYqiUjLdsgCN8woiARgAmAGZq9Mzs4QKiyhpqoA)

```js
/**
 * @templateT
 * @param{T}x - A generic parameter that flows through to the return type
 * @returns{T}
 */
functionid(x) {
returnx;
}

consta = id("string");
constb = id(123);
constc = id({});
```

Use comma or multiple tags to declare multiple type parameters:

```js
/**
 * @templateT,U,V
 * @templateW,X
 */
```

You can also specify a type constraint before the type parameter name.
Only the first type parameter in a list is constrained:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXApgWwA4BsCGTIG8DO8ATgJYB2A5gL6QDSkAtHZMgK6GQBGikmkhpSpAD2RfsXIVI6EkiKZ0UGAhQZsPXLn6JSw9gAoAlAC5xgqVRoBlHST34FJAF6ZO6HkzYcAFpgBuPHz4tvYsiPDewgAmSnComPLIeLQ0ANaIAJ6xsPGJeDa67I4ubog0wpwAVogAxvBKwOAAZqxkdXZk2oUOMk6I+ukZADQiVbXwhnhQkMDAkAD8i-PgVEA)

```js
/**
 * @template {string} K - K must be a string or string literal
 * @template {{ serious(): string }} Seriousalizable - must have a serious method
 * @param{K}key
 * @param{Seriousalizable}object
 */
functionseriousalize(key, object) {
// ????
}
```

Finally, you can specify a default for a type parameter:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcFMFsAOAbAhncBtAKgXgPYBGAVrAMbQC64IwAUGWgM5PgDCqZAFrOAN51wQ8KAiREqAE6p4-bAF9wASwB2S6EtTIa9YeDL4VTaJICuFfJIAUq9ZuQBKfoOHy6b5LGj7wucCtgAd3ZOHisHIA)

```js
/** @template [T=object] */
classCache {
/** @param{T}initial */
constructor(initial) {
    }
}
letc = newCache()
```

### `@satisfies` {#satisfies}

`@satisfies` provides access to the postfix [operator `satisfies`](/typescript/5.1/whats-new/typescript-4-9) in TypeScript. Satisfies is used to declare that a value implements a type but does not affect the type of the value.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygIwGYBsAGAUCBAC6IC0AxgBaTkDWBAVA3qA8QJ4AOkAJpAGagA3gCJqAG3FxQAdwTieI0AB9QIgBKRJcADSz5igL6gA6lvJwAtpACykRIgCGAc0gsGwPIzbgnRAJaI-P72wmbiFtZ2Di6Qxh54FgB2iESg1jGuoAC8ahJS+tAKIgRgoOWgAHoA-F7ATBB+gcGhQuGRtvZOrvGeyamg-I7+4v5JztHdkDlqmtqFCgCEJd4c3GHmVp2Zcax9cClpGVMAqohjzgAqXNO5YloFckWKpRXlNUA)

```js
// @ts-check
/**
 * @typedef{"hello world" | "Hello, world"}WelcomeMessage
 */

/** @satisfies {WelcomeMessage} */
constmessage = "hello world"

const message: "hello world"

/** @satisfies {WelcomeMessage} */
constfailingMessage = "Hello world!"

/** @type{WelcomeMessage} */
constmessageUsingType = "hello world"

const messageUsingType: WelcomeMessage
```

```text {filename="Generated error"}
Type '"Hello world!"' does not satisfy the expected type 'WelcomeMessage'.
```

## Classes {#classes-1}

Classes can be declared as ES6 classes.

[Try this code ↗](https://www.typescriptlang.org/play#code/MYGwhgzhAEDC0G8BQ1oHoBUGWo9AAgA5gBOYAtogHYCu5ARgKYkC+0AJmAC5g7QZocwAPZUIXEjWBdhJABSceASkR90aaIRLDCzLgE9oB3TGBgq0JtACWVAGbMSjdmq4ALaxAB0VCo2gAvNAARHbCwsEA3EhqaBqy0BCMXNCMAB6EINbA1lwg+rFYBMb+COIktgDm0AA+0LQgIGwCrh7eXLkg-kENINGx8SSJ1uSZhuZUwjxczgA0NnZGboz6AOROicmpIEkA7stOhXj4JdR0TKz8gqio7p5eENYAXoz9N0v3trnWYFkvCtwwEpIupoABlNzCGggdipEjaEjzL4dX7PZipDKMaQwMCJCRVHAsHCYbA3Y7EMiUMr4qiVNgQPgtVDIn5-brQOw0KjSayiaByCAqZDvO7eR4vQKJLxdWnuaKoFjRIlIABupGgwElVEYuzgcgADMCYnE4IlIdDYaJ8pZ-GZGs5oLtcm56jr5vQaFwkCamGYaEkbClPNAAFJgNVg4AVQhceaimw4xrCXYO8wuE0iMTWdjMVMWVbmNZeVXqpwQaEpIKwOQARmBQA)

```js
class C {
/**
   * @param{number}data
   */
constructor(data) {
// property types can be inferred
this.name = "foo";

// or set explicitly
/** @type{string | null} */
this.title = null;

// or simply annotated, if they're set elsewhere
/** @type{number} */
this.size;

this.initialize(data); // Should error, initializer expects a string
  }
/**
   * @param{string}s
   */
initialize = function (s) {
this.size = s.length;
  };
}

varc = newC(0);

// C should only be called with new, but
// because it is JavaScript, this is allowed and
// considered an 'any'.
varresult = C(1);
```

They can also be declared as constructor functions; use [`@constructor`](/typescript/5.1/javascript/jsdoc-supported-types#constructor) along with [`@this`](/typescript/5.1/javascript/jsdoc-supported-types#this) for this.

### Property Modifiers {#property-modifiers}



`@public`, `@private`, and `@protected` work exactly like `public`, `private`, and `protected` in TypeScript:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGYAsBGAUCBAC6IC0AxgBaTkDWee5ANgIaKKgDCL0oA3nlChycAHaIi0AK7kiCABQBKfoKGhgAKg0QADtACWANxZFIoDcFVCilfYgB0+gCaRRRfQDN9MUAF5QOAAMgQDcqgC+DEJ6+m4Aki5unt7QSipqwmKIcEyQ9kxwAObyNnaOie5eMIphQpGRjFlEwn6gopAA7lw8SmEi4jl5BcXk5a6VKTVAA)

```js
// @ts-check

classCar {
constructor() {
/** @private */
this.identifier = 100;
  }

printIdentifier() {
console.log(this.identifier);
  }
}

constc = newCar();
console.log(c.identifier);
```

```text {filename="Generated error"}
Property 'identifier' is private and only accessible within class 'Car'.
```

- `@public` is always implied and can be left off, but means that a property can be reached from anywhere.
- `@private` means that a property can only be used within the containing class.
- `@protected` means that a property can only be used within the containing class, and all derived subclasses, but not on dissimilar instances of the containing class.

`@public`, `@private`, and `@protected` do not work in constructor functions.

### `@readonly` {#readonly}

The `@readonly` modifier ensures that a property is only ever written to during initialization.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwFYAsAGAUCBAC6IC0AxgBaTkDWee5ANgIaKKgDCL0oA3nlChycAHaIi0AK7kiCABQBKfoKGhgAKg0RokFgBMxTAJ6gNwVUKKUAlogB0N-ZFFEbAMxsxQAXlABGHBwAblUAXwYhAAdoG1cASWdXDy9oJRU1YTFEOCZIeyY4AHN5aztHJLdPGEVQoQiIxmyiYV9QUUgAdy4eJVCRcVz8wpLyCpcq1NqgA)

```js
// @ts-check

classCar {
constructor() {
/** @readonly */
this.identifier = 100;
  }

printIdentifier() {
console.log(this.identifier);
  }
}

constc = newCar();
console.log(c.identifier);
```

### `@override` {#override}

`@override` works the same way as in TypeScript; use it on methods that override a method from a base class:

[Try this code ↗](https://www.typescriptlang.org/play#code/KYDwDg9gTgLgBAYwDYEMDOa4GE4G8BQccAtgBQCUecAvvrcupgCJygzAB2AJpjgUQHoAVELgABCADdgUKAEsuwOEIGESFKrWpA)

```js
export class C {
m() { }
}
classDextendsC {
/** @override */
m() { }
}
```

Set `noImplicitOverride: true` in tsconfig to check overrides.

### `@extends` {#extends}

When JavaScript classes extend a generic base class, there is no JavaScript syntax for passing a type argument. The `@extends` tag allows this:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXApgWwA4BsCGTIBUoyyIAeSAdgCYDOkA3gMqLwA8uAfAL4HDgDGWVGvQD2AJ3iYARukSN4kEuWqQ5dKJGDBIAOl3gOQA)

```js
/**
 * @templateT
 * @extends{Set<T>}
 */
classSortableSetextendsSet {
// ...
}
```

Note that `@extends` only works with classes. Currently, there is no way for a constructor function to extend a class.

### `@implements` {#implements}

In the same way, there is no JavaScript syntax for implementing a TypeScript interface. The `@implements` tag works just like in TypeScript:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEEsFsAcA2BTWyB2AXAzuA3gAoBO0WAvuCMAFADGiAhtrgCrIAemAQgPY8DW+auHDwSWABQBKISJHBg4FgHkAIsuHgy1MkA)

```js
/** @implements {Print} */
classTextBook {
print() {
// TODO
  }
}
```

### `@constructor` {#constructor}

The compiler infers constructor functions based on this-property assignments, but you can make checking stricter and suggestions better if you add a `@constructor` tag:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEFYCdsHtsUAuUAJgGYAWAVjKoA4MAqJtUJiSPAOxQBdsAV0h8CbDuAAOAQ2zSAtqADe3QfIBGOAL6gAJtL7TxwNADNB3EQEseoAMIAKfYYCUytqEyT8knHwCeoAG+KKCQ0tygmqBW3KY42LC6HnzQVigAdNwKsKAAvKAARKZ4eIUA3GgemASgKLB8oLAAHpIANlaQVnxt-tUsEMG5SvzYsQDmoAA+oKptbTpMJqBBaZl83W25BXNtldVgtShW8u2BEdx4hnxJADQxpquw-gDkiXUNTW31AO5wif0JENlKoNNp2MtVukMscAF6wfYrVLQ2LdKzSDrwpwGaQuSpaZisdgQGRyRQjAQTHToCFoOwZbxXK7+XwZVEbDFWeH5UDmSwbWwOFBuJQpNYwrnbOoZLbccapfH7ABusjCPO4sB+9gcAAY8WhIBL4crVYkUII2o0Co4AIx4oA)

```js
/**
 * @constructor
 * @param{number}data
 */
functionC(data) {
// property types can be inferred
this.name = "foo";

// or set explicitly
/** @type{string | null} */
this.title = null;

// or simply annotated, if they're set elsewhere
/** @type{number} */
this.size;

this.initialize(data);
}
/**
 * @param{string}s
 */
C.prototype.initialize = function (s) {
this.size = s.length;
};

varc = newC(0);
c.size;

varresult = C(1);
Value of type 'typeof C' is not callable. Did you mean to include 'new'?2348Value of type 'typeof C' is not callable. Did you mean to include 'new'?
```

```text {filename="Generated error"}
Argument of type 'number' is not assignable to parameter of type 'string'.
```

> Note: Error messages only show up in JS codebases with [a JSConfig](/typescript/5.1/project-configuration/tsconfig-json) and [`checkJs` ↗](https://www.typescriptlang.org/tsconfig.html#checkJs) enabled.
> 

With `@constructor`, `this` is checked inside the constructor function `C`, so you will get suggestions for the `initialize` method and an error if you pass it a number. Your editor may also show warnings if you call `C` instead of constructing it.

Unfortunately, this means that constructor functions that are also callable cannot use `@constructor`.

### `@this` {#this}

The compiler can usually figure out the type of `this` when it has some context to work with. When it doesn’t, you can explicitly specify the type of `this` with `@this`:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAFgSwM6QN4AkAqBZAGQFEAbAUwFsyA7eAXyhlgAcBDAJ1YsxDsjMbBwAMwCu1AMbxkAe2qQJrEiQBGrCQGsAYjPYFW8MuwAUZAJSYokJGgB0EkshrwsZZAHNE8SAF5IbdlQyAElaUzMAbkhgYEhURBlREgATSBUySGFkajIAQnA6IA)

```js
/**
 * @this{HTMLElement}
 * @param{*}e
 */
functioncallbackForLater(e) {
this.clientHeight = parseInt(e); // should be fine!
}
```

## Documentation {#documentation-1}

### `@deprecated` {#deprecated}



When a function, method, or property is deprecated you can let users know by marking it with a `/** @deprecated */` JSDoc comment. That information is surfaced in completion lists and as a suggestion diagnostic that editors can handle specially. In an editor like VS Code, deprecated values are typically displayed in a strike-through style .

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDsHsFECd7XgZwFDAFSYgEwKYAO8+AxgIYAu+uomwap0kKlo5hAlgGoCMoAXlABvAL4BuRs1bsu3AEyCREtGg49JIUAD0APqqA)

```js
/** @deprecated */
constapiV1 = {};
constapiV2 = {};

apiV;
- apiV1
- apiV2
```

### `@see` {#see}

`@see` lets you link to other names in your program:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAQg9gDwDwBUB8UC8UDeVgBcUKUAvgFAD0AVNVAAIDOE08CUAZnAE5QCWAWzAAbCAIgA7YAENgfOBKgATCDL7DGUapXKhIsRHw4hUGbHgDaAaX6KA1hBBwOxALpE2psgG4gA)

```ts
type Box<T> = { t: T }
/** @seeBox for implementation details */
typeBoxify<T> = { [KinkeyofT]: Box<T> };
```

Some editors will turn `Box` into a link to make it easy to jump there and back.

### `@link` {#link}

`@link` is like `@see`, except that it can be used inside other tags:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAQg9gDwDwBUB8UC8UDeVgBcUKUAvgFAD0AVNVAAIBOEwArowHYDOUAgrvQA2ASw4BrWIlJQAxnA7AAhqNEBzfAAtoYRY0UBbFhEYA6KNUrkAZqw4zgw+VABGiJAFU0AClZF3ASiJ4ZE9ccigoZjZOXHwiVjIAbnJSIA)

```ts
type Box<T> = { t: T }
/** @returns A {@linkBox} containing the parameter. */
functionbox<U>(u: U): Box<U> {
return { t:u };
}
```

## Other {#other-1}

### `@enum` {#enum}

The `@enum` tag allows you to create an object literal whose members are all of a specified type. Unlike most object literals in JavaScript, it does not allow other members.
`@enum` is intended for compatibility with Google Closure’s `@enum` tag.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEFMDsFcC24DeDECNoCcC+4RgAoAYwHtYBnAF3ACkBlAETJIeoENrpwBeVIuHAAhaAHMAlrFhSxAeQBmAGSnQAXOAAMAGkHgGHAO4BBGjgmUA1hoCMuoQYBusgMJlEiONUoaATLtwAbiIiRhY2Tm4AOgMTM2wLS0CgA)

```js
/** @enum {number} */
constJSDocState = {
BeginningOfLine:0,
SawAsterisk:1,
SavingComments:2,
};

JSDocState.SawAsterisk;
```

Note that `@enum` is quite different from, and much simpler than, TypeScript’s `enum`. However, unlike TypeScript’s enums, `@enum` can have any type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEFMDsFcC24DeAzesDGAXAlgPawAUCiARtAE4CUAXOGZVQL7gjABQWRAzjuACyAQxwALAGKYsvcAF5UncOGEATVQEYGpGvIB8jcAGpwGgDRLweVdti65BgLSwLy3vHJbwO-YcemLFgBuTk4RcSlsXgA6NU0goA)

```js
/** @enum {function(number): number} */
constMathFuncs = {
add1: (n) =>n + 1,
id: (n) => -n,
sub1: (n) =>n - 1,
};

MathFuncs.add1;
```

### `@author` {#author}

You can specify the author of an item with `@author`:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUh1BTANgYwPYFt6QC6sgQwHd4BnDeAOmxKhgAF8BXbAC1QCdIBJfAO0gCCxMpkgAeAJYV86acPJ14ADxkAHRJTToAfLWBA)

```ts
/**
 * Welcome to awesome.ts
 * @authorIan Awesome <i.am.awesome@example.com>
 */
```

Remember to surround the email address with angle brackets.
Otherwise, `@example` will be parsed as a new tag.

### Other supported patterns {#other-supported-patterns}

[Try this code ↗](https://www.typescriptlang.org/play#code/MYGwhgzhAEBiD29oG8C+AoA9J6BafwArgC767oBuYATtBPALYCmA8gEYBW0AvCutNEwAqIfwFDoAAQAONMAxQRi1AJYA7AOapos6vICMeaACkAygBF4wGPDU7q8aU2rEAntEgQVGtczXEYAHd4agBrMWghTDEADwAuaAAzQjVgYhVbaAAKXQMAShRUABp0VABudCwRfgkAQRgAEyQOCCbraEyqVTA2ECYPKG9fJn8IGqlqJmJCajtkAHV1JsCMSOi+4jpGJlgU4B4kvfTMrIK0CqrRSOhatQboUE9oZmIAC3gGseuZOQVkJVUmm0GkmU3UGmgABVXv0QUwwZpoMQkIQIEwatEEPAAHTSBzItxObEQMCuAASKgOWThCI0BW4AD4HrZ6H1sSB4BosgAiCkAQm5eQuwiudTuHmoDkCh1SxzsTBieKYgxZ4x+ej+akIDDYzm0MSMtWehBA6WkIBUzgx6A2z1ctUl8GlvCyMXpTINEhiwuq13mrxUwFezyYYDUMBUm2CYRgiRCMrSGTswEY0lsIwC0HUJlMAA0kYg1bk-sgwAkAeCitA2AktTq9do8Y4YLhoKZtjpftaukl9i6m9IIO7oAAeBoqCgM5ADiDYsDYoM0WrELIABjyqBHmHHk59opunY1U2cDzD1f6YAe4CgzPDykIaRCVdR4OgAHFEBo+tAAMIciAzP0ECuP4YAxNiNRFr8KDIGoTCBFk2JITQGgQAkYauAA2gAunkCTwJwTBpKg2g-kY0L9I8N7ItAkwaCoShWms6DJLKSa0Uw9GMdQf6eFkP5nBglxQRqKBIcSyjgo2hitkaADkkxKHJEoQlkNB6K4BTwIkdCSZos7ZMoobEEw9yQNAckYXJeTWqxiaZIkaj6KuOT6IJlQiiJ8hiUhFZAjoMkHgpyrEMpKHZOppJaTpfmodihmTGAJlmTAllqK41m2Uc7GOQAnK5ZxiJM0yzAF2IcPA6inBUqBAA)

```js
var someObj = {
/**
   * @param{string}param1 - JSDocs on property assignments work
   */
x:function (param1) {},
};

/**
 * As do jsdocs on variable assignments
 * @return{Window}
 */
letsomeFunc = function () {};

/**
 * And class methods
 * @param{string}greeting The greeting to use
 */
Foo.prototype.sayHi = (greeting) =>console.log("Hi!");

/**
 * And arrow function expressions
 * @param{number}x - A multiplier
 */
letmyArrow = (x) =>x * x;

/**
 * Which means it works for function components in JSX too
 * @param{{a: string, b: number}}props - Some param
 */
varfc = (props) =><div>{props.a.charAt(0)}</div>;

/**
 * A parameter can be a class constructor, using Google Closure syntax.
 *
 * @param{{new(...args: any[]): object}}C - The class to register
 */
functionregisterClass(C) {}

/**
 * @param{...string}p1 - A 'rest' arg (array) of strings. (treated as 'any')
 */
functionfn10(p1) {}

/**
 * @param{...string}p1 - A 'rest' arg (array) of strings. (treated as 'any')
 */
functionfn9(p1) {
returnp1.join();
}
```

### Unsupported patterns {#unsupported-patterns}

Postfix equals on a property type in an object literal type doesn’t specify an optional property:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4cgQwFyQDO8ATgJYB2A5gDSQBGBFArgLb2okC8kAvr1BDBwANxwlIAdxIB7agG5woCNEgBVQumQziAMzIAPSAEdmqYmTmQr8ABZbZaEkkgUcrdJWKocAEzyCcEhomNj4RKSUtAwA-ExsHBL8gsJiEuRUtvDyQA)

```js
/**
 * @type{{ a: string, b: number= }}
 */
varwrong;
/**
 * Use postfix question on the property name instead:
 * @type{{ a: string, b?: number }}
 */
varright;
```

Nullable types only have meaning if [`strictNullChecks` ↗](https://www.typescriptlang.org/tsconfig.html#strictNullChecks) is on:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4H4B2BXAWwCNUAnAXyhgHUBLeAC0gGd5TaBjeAOTwBs+AYQaoOAa2YAuSGzzpIAWgWR8xMpAA+K-nyqQ6jFm048dw0ROkAzAIZ9m6JdrWkqwcADcbpbQJtE+VABuIA)

```js
/**
 * @type{?number}
 * With strictNullChecks: true  -- number | null
 * With strictNullChecks: false -- number
 */
varnullable;
```

The TypeScript-native syntax is a union type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4DsCuBbAI1QCdIAfSXAGyoF8oYB1AS3gAtIBneY5gY3gA5HDQDCbVHwDWnAFyQeOdJAC0KyviKkK1Kg0gt2XHvyEiq4yTPkAzAIZVO6NRsIkGwcADc7pHFmYAeyxhGjsCKlQAbiA)

```js
/**
 * @type{number | null}
 * With strictNullChecks: true  -- number | null
 * With strictNullChecks: false -- number
 */
varunionNullable;
```

Non-nullable types have no meaning and are treated just as their original type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAXAngBwKaQN4EIB2BXAWwCNUAnAXyhgCk8BneSACwEM7Ik1J9iyrhwANxaluAe1IEWAGwDcQA)

```js
/**
 * @type{!number}
 * Just has type number
 */
varnormal;
```

Unlike JSDoc’s type system, TypeScript only allows you to mark types as containing null or not.
There is no explicit non-nullability — if strictNullChecks is on, then `number` is not nullable.
If it is off, then `number` is nullable.

### Unsupported tags {#unsupported-tags}

TypeScript ignores any unsupported JSDoc tags.

The following tags have open issues to support them:

- `@const` ([issue #19672 ↗](https://github.com/Microsoft/TypeScript/issues/19672))
- `@inheritdoc` ([issue #23215 ↗](https://github.com/Microsoft/TypeScript/issues/23215))
- `@memberof` ([issue #7237 ↗](https://github.com/Microsoft/TypeScript/issues/7237))
- `@yields` ([issue #23857 ↗](https://github.com/Microsoft/TypeScript/issues/23857))
