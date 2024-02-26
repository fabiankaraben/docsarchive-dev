---
linkTitle: "Typeof Type Operator"
title: "TypeScript: Documentation - Typeof Type Operator"
description: "Using the typeof operator in type contexts."
weight: 4
type: docs
---

# Typeof Type Operator

## The `typeof` type operator {#the-typeof-type-operator}

JavaScript already has a `typeof` operator you can use in an *expression* context:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAUCcEsDsBcDOoBEj41gcxQKAMYD2sihANgKYB0ZhWAFPAJ4AOFhAZqgBIVm2gA7oUhkAJigCUAbiA)

```ts
// Prints "string"
console.log(typeof"Hello world");
```

TypeScript adds a `typeof` operator you can use in a *type* context to refer to the *type* of a variable or property:

[Try this code ↗](https://www.typescriptlang.org/play#code/DYUwLgBAzhC8ECIAWJjAPYINwChSQDsAuCMATwAcR0AzaXAegYggD0B+IA)

```ts
let s = "hello";
letn: typeofs;

let n: string
```

This isn’t very useful for basic types, but combined with other type operators, you can use `typeof` to conveniently express many patterns.
For an example, let’s start by looking at the predefined type `ReturnType<T>`.
It takes a *function type* and produces its return type:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBACgThAJgSwMYENjQLxQBQAeAXFAK4B2A1uQPYDu5AlFNgHxQBGNNANhOuQDcAKFCQoAaRZQAShGCk45ACrgIAHnhI0mCKxEB6A1BMA9APxA)

```ts
type Predicate = (x: unknown) => boolean;
typeK = ReturnType<Predicate>;

type K = boolean
```

If we try to use `ReturnType` on a function name, we see an instructive error:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwHYAsBOAUAGYCuAdgMYAuAlnCaAQBQCUoA3nqKNJBUdHa1AAPVAEYADABpQAT1QBmUAF8A3HiV4KMgA6RQABVABeUACUefEgBUdkADwEAfCqA)

```ts
function f() {
return { x:10, y:3 };
}
typeP = ReturnType<f>;
```

```text {filename="Generated error"}
'f' refers to a value, but is being used as a type here. Did you mean 'typeof f'?
```

Remember that *values* and *types* aren’t the same thing.
To refer to the *type* that the *value `f`* has, we use `typeof`:

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABMAFASkQbwFCMQJwFMoR8lNEAPALkQEYAGAGkQE9aBmRAXwG5tu2KKwAOhRAAVEAXkQAlYqTAAVUYQA8wsXGDIAfPwD0hvIgB6AfiA)

```ts
function f() {
return { x:10, y:3 };
}
typeP = ReturnType<typeoff>;

type P = {
    x: number;
    y: number;
}
```

### Limitations {#limitations}

TypeScript intentionally limits the sorts of expressions you can use `typeof` on.

Specifically, it’s only legal to use `typeof` on identifiers (i.e. variable names) or their properties.
This helps avoid the confusing trap of writing code you think is executing, but isn’t:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygIwAYMFYBQATSAYwBsBDaSUIuAO0QBdQBbRAcwCM4APVACgAO8ZgIapG0AJa02ASlABeAHygucEpDK0A3LhCgGATwFVWnHotBbDu-QFoHRAK4MHdvWACym2kwZxQJ0QqBVAAJUgGJ2haABVjSAAeIxM4ADMWdi5uJVwNJkQACzgnEnwAYToGaSdIVBTIdMzzbj4AIgBBSlBDEtBEaKpep1AAdy0-AJpfGsgAfjbZbSA)

```ts
// Meant to use = ReturnType<typeof msgbox>
letshouldContinue: typeofmsgbox("Are you sure you want to continue?");
```

```text {filename="Generated error"}
',' expected.
```
