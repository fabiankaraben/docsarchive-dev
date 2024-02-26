---
linkTitle: "TypeScript 1.3"
title: "TypeScript: Documentation - TypeScript 1.3"
description: "TypeScript 1.3 Release Notes"
weight: 39
type: docs
---

# TypeScript 1.3

## Protected {#protected}

The new `protected` modifier in classes works like it does in familiar languages like C++, C#, and Java. A `protected` member of a class is visible only inside subclasses of the class in which it is declared:

```ts
class Thing {
protecteddoSomething() {
/* ... */
  }
}

classMyThingextendsThing {
publicmyMethod() {
// OK, can access protected member from subclass
this.doSomething();
  }
}
vart = newMyThing();
t.doSomething(); // Error, cannot call protected member from outside class
```

## Tuple types {#tuple-types}

Tuple types express an array where the type of certain elements is known, but need not be the same. For example, you may want to represent an array with a `string` at position 0 and a `number` at position 1:

```ts
// Declare a tuple type
varx: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error
```

When accessing an element with a known index, the correct type is retrieved:

```ts
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

Note that in TypeScript 1.4, when accessing an element outside the set of known indices, a union type is used instead:

```ts
x[3] = "world"; // OK
console.log(x[5].toString()); // OK, 'string' and 'number' both have toString
x[6] = true; // Error, boolean isn't number or string
```
