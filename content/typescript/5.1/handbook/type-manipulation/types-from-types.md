---
linkTitle: "Creating Types from Types"
title: "TypeScript: Documentation - Creating Types from Types"
description: "An overview of the ways in which you can create more types from existing types."
weight: 1
type: docs
prev: /typescript/5.1/handbook/objects
---

# Creating Types from Types

TypeScript’s type system is very powerful because it allows expressing types *in terms of other types*.

The simplest form of this idea is generics. Additionally, we have a wide variety of *type operators* available to use.
It’s also possible to express types in terms of *values* that we already have.

By combining various type operators, we can express complex operations and values in a succinct, maintainable way.
In this section we’ll cover ways to express a new type in terms of an existing type or value.

- [Generics](/typescript/5.1/handbook/type-manipulation/generics) - Types which take parameters
- [Keyof Type Operator](/typescript/5.1/handbook/type-manipulation/keyof-types) - Using the `keyof` operator to create new types
- [Typeof Type Operator](/typescript/5.1/handbook/type-manipulation/typeof-types) - Using the `typeof` operator to create new types
- [Indexed Access Types](/typescript/5.1/handbook/type-manipulation/indexed-access-types) - Using `Type['a']` syntax to access a subset of a type
- [Conditional Types](/typescript/5.1/handbook/type-manipulation/conditional-types) - Types which act like if statements in the type system
- [Mapped Types](/typescript/5.1/handbook/type-manipulation/mapped-types) - Creating types by mapping each property in an existing type
- [Template Literal Types](/typescript/5.1/handbook/type-manipulation/template-literal-types) - Mapped types which change properties via template literal strings
