---
linkTitle: "Keyof Type Operator"
title: "TypeScript: Documentation - Keyof Type Operator"
description: "Using the keyof operator in type contexts."
weight: 3
type: docs
---

# Keyof Type Operator

## The `keyof` type operator {#the-keyof-type-operator}

The `keyof` operator takes an object type and produces a string or numeric literal union of its keys.
The following type `P` is the same type as `type P = "x" | "y"`:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBACg9gSwHbCgXigbygDwFxRICuAtgEYQBOA3FCAceVVAL7UBQoks6UA1hBBwAZrEQoOAeklRZAPQD8QA)

```ts
type Point = { x: number; y: number };
type P = keyof Point;
    
type P = keyof Point
```

If the type has a `string` or `number` index signature, `keyof` will return those types instead:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAggTnAhiAlgZwBZQLxQN5QDaAdgFxTECuAtgEYRwC65lxA1sQPYDuxUAvgG4AUKEiwcUNhBCcAZrATJ0GEQHo1ULQD0A-MNHhoAWURgVkgoTbk0wOCmIBzZlFqdOAGwiI+Qw+LGktKyCqbmmOqaOrpAA)

```ts
type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish;
    
type A = number
 
type Mapish = { [k: string]: boolean };
type M = keyof Mapish;
    
type M = string | number
```

Note that in this example, `M` is `string | number` — this is because JavaScript object keys are always coerced to a string, so `obj[0]` is always the same as `obj["0"]`.

`keyof` types become especially useful when combined with mapped types, which we’ll learn more about later.
