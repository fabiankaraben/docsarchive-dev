---
linkTitle: "Symbols"
title: "TypeScript: Documentation - Symbols"
description: "Using the JavaScript Symbol primitive in TypeScript"
weight: 14
type: docs
canonical: /typescript/5.2/reference/symbols
---

# Symbols

## `unique symbol` {#unique-symbol}

To enable treating symbols as unique literals a special type `unique symbol` is available. `unique symbol` is a subtype of `symbol`, and are produced only from calling `Symbol()` or `Symbol.for()`, or from explicit type annotations. This type is only allowed on `const` declarations and `readonly static` properties, and in order to reference a specific unique symbol, you’ll have to use the `typeof` operator. Each reference to a unique symbol implies a completely unique identity that’s tied to a given declaration.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygIwGYMCYBQATSAYwBsBDaSUIuAO0QBdREBPAWzVQFdaBLARy5VWbAEZwSAbly4QzdtmplaoOiRahRVMtTqNlTSgDMYkWkUgA6XCUhMR2bn0HD24kqAC8oAMpuJABQAlNKyYADqCADWiKAAtKDGMLEMcKA6PAJC8mISADSaXEy8DLG8hLQMJRq8KbyQ+KCpoADkImgt1rb27BioDCwADpBwRjloXuOhcgCCJIhpAO7RiNakZIixAMKgAN64oMwMZFVEiZBk+GoaPsenfrkkTlmuj5MP7sHSAL5AA)

```ts
declare const sym1: unique symbol;

// sym2 can only be a constant reference.
letsym2: uniquesymbol = Symbol();

// Works - refers to a unique symbol, but its identity is tied to 'sym1'.
letsym3: typeofsym1 = sym1;

// Also works.
classC {
staticreadonlyStaticSymbol: uniquesymbol = Symbol();
}
```

```text {filename="Generated error"}
A variable whose type is a 'unique symbol' type must be 'const'.
```

Because each `unique symbol` has a completely separate identity, no two `unique symbol` types are assignable or comparable to each other.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGYBsB2AUAMZwB2iALqIgJ4C2aoAvKAMq0BGcANgBQCUA3IRLlKtDIxbsufQXgCWAM1DdqdRgyaqMvUAG88oUCFAA6M3gC+QA)

```ts
const sym2 = Symbol();
constsym3 = Symbol();

if (sym2 === sym3) {
// ...
}
```

```text {filename="Generated error"}
This comparison appears to be unintentional because the types 'typeof sym2' and 'typeof sym3' have no overlap.
```

## Well-known Symbols {#well-known-symbols}

In addition to user-defined symbols, there are well-known built-in symbols.
Built-in symbols are used to represent internal language behaviors.

Here is a list of well-known symbols:

### `Symbol.asyncIterator` {#symbolasynciterator}

A method that returns async iterator for an object, compatible to be used with for await..of loop.

### `Symbol.hasInstance` {#symbolhasinstance}

A method that determines if a constructor object recognizes an object as one of the constructor’s instances. Called by the semantics of the instanceof operator.

### `Symbol.isConcatSpreadable` {#symbolisconcatspreadable}

A Boolean value indicating that an object should be flattened to its array elements by Array.prototype.concat.

### `Symbol.iterator` {#symboliterator}

A method that returns the default iterator for an object. Called by the semantics of the for-of statement.

### `Symbol.match` {#symbolmatch}

A regular expression method that matches the regular expression against a string. Called by the `String.prototype.match` method.

### `Symbol.replace` {#symbolreplace}

A regular expression method that replaces matched substrings of a string. Called by the `String.prototype.replace` method.

### `Symbol.search` {#symbolsearch}

A regular expression method that returns the index within a string that matches the regular expression. Called by the `String.prototype.search` method.

### `Symbol.species` {#symbolspecies}

A function valued property that is the constructor function that is used to create derived objects.

### `Symbol.split` {#symbolsplit}

A regular expression method that splits a string at the indices that match the regular expression.
Called by the `String.prototype.split` method.

### `Symbol.toPrimitive` {#symboltoprimitive}

A method that converts an object to a corresponding primitive value.
Called by the `ToPrimitive` abstract operation.

### `Symbol.toStringTag` {#symboltostringtag}

A String value that is used in the creation of the default string description of an object.
Called by the built-in method `Object.prototype.toString`.

### `Symbol.unscopables` {#symbolunscopables}

An Object whose own property names are property names that are excluded from the ‘with’ environment bindings of the associated objects.
