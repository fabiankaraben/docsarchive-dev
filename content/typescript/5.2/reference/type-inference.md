---
linkTitle: "Type Inference"
title: "TypeScript: Documentation - Type Inference"
description: "How code flow analysis works in TypeScript"
weight: 14
type: docs
---

# Type Inference

In TypeScript, there are several places where type inference is used to provide type information when there is no explicit type annotation. For example, in this code

[Try this code ↗](https://www.typescriptlang.org/play/#code/DYUwLgBAHhC8EGYDcAoA9GiEB6B+IA)

```ts
let x = 3;
   
let x: number
```

The type of the `x` variable is inferred to be `number`.
This kind of inference takes place when initializing variables and members, setting parameter default values, and determining function return types.

In most cases, type inference is straightforward.
In the following sections, we’ll explore some of the nuances in how types are inferred.

## Best common type {#best-common-type}

When a type inference is made from several expressions, the types of those expressions are used to calculate a “best common type”. For example,

[Try this code ↗](https://www.typescriptlang.org/play/#code/DYUwLgBAHhC8EG0AMAaCBGNA7ArsYAugNwBQA9GRBAHoD8QA)

```ts
let x = [0, 1, null];
   
let x: (number | null)[]
```

To infer the type of `x` in the example above, we must consider the type of each array element.
Here we are given two choices for the type of the array: `number` and `null`.
The best common type algorithm considers each candidate type, and picks the type that is compatible with all the other candidates.

Because the best common type has to be chosen from the provided candidate types, there are some cases where types share a common structure, but no one type is the super type of all candidate types. For example:

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgCCAdvALY6gDeAvkSWaAEoAW8VA9qDwAPaHioATctToNGBUKG6kAEv1hU0cAK54A3AVbFS5AKLY8AB2VVoQ0eKmUa9bEwVLSAFVjaqAay1ffUM2E1AAZSpMfzx7MUlpFzkPZUgAGTwAc0g0LFwQ1hBQAFoyxG1oMpKCCzsAL35BAF5QAG0qPAB3Ll4BAAoASgAaUE6e8ysbaCHR8cjo2KGAXQNixVAAPQB+IA)

```ts
let zoo = [new Rhino(), new Elephant(), new Snake()];
    
let zoo: (Rhino | Elephant | Snake)[]
```

Ideally, we may want `zoo` to be inferred as an `Animal[]`, but because there is no object that is strictly of type `Animal` in the array, we make no inference about the array element type.
To correct this, explicitly provide the type when no one type is a super type of all other candidates:

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgCCAdvALY6gDeAvkSWaAEoAW8VA9qDwAPaHioATctToNGBUKG6kAEv1hU0cAK54A3AVbFS5AKLY8AB2VVoQ0eKmUa9bEwVLSAFVjaqAay1ffUM2E1AAZSpMfzx7MUlpFzkPZUgAGTwAc0g0LFwQ1hBQAFoyxG1oMpKCCzsAL35+NBlXAG0AXVAAXlA2qjwAdy5eAQAKAEoAGlAB4fMrG2hJmbnI6NjJjoNixVAAPQB+IA)

```ts
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
    
let zoo: Animal[]
```

When no best common type is found, the resulting inference is the union array type, `(Rhino | Elephant | Snake)[]`.

## Contextual Typing {#contextual-typing}

Type inference also works in “the other direction” in some cases in TypeScript.
This is known as “contextual typing”. Contextual typing occurs when the type of an expression is implied by its location. For example:

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYME4BQB3ASwDsATOfAOjmIFs4BXRSc-Y0AXlADMHiBjAC6EaoABT0mkAKIA3SMUEBKUAG9coUPxqI4AG0iU9cAOYTGzOQsGUARg0GCaSgNwatO-YeNnJl+YqUANYAhsQmIfBwrrgAvi5AA)

```ts
window.onmousedown = function (mouseEvent) {
  console.log(mouseEvent.button);
  console.log(mouseEvent.kangaroo);
};
```

```text {filename="Generated error"}
Property 'kangaroo' does not exist on type 'MouseEvent'.
```

Here, the TypeScript type checker used the type of the `Window.onmousedown` function to infer the type of the function expression on the right hand side of the assignment.
When it did so, it was able to infer the [type](https://developer.mozilla.org/docs/Web/API/MouseEvent) of the `mouseEvent` parameter, which does contain a `button` property, but not a `kangaroo` property.

This works because window already has `onmousedown` declared in its type:

```ts
// Declares there is a global variable called 'window'
declare var window: Window & typeof globalThis;

// Which is declared as (simplified):
interface Window extends GlobalEventHandlers {
  // ...
}

// Which defines a lot of known handler events
interface GlobalEventHandlers {
  onmousedown: ((this: GlobalEventHandlers, ev: MouseEvent) => any) | null;
  // ...
}
```

TypeScript is smart enough to infer types in other contexts as well:

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYME4BQB3ASwDsATOfAOjmMQGN4AbR0AXlADMBXYugF0I1QACi6EAogDdIxPgEpQAb1yhQdGojiNIlRnADmoidNmUARlz58acgNy4AvraA)

```ts
window.onscroll = function (uiEvent) {
  console.log(uiEvent.button);
};
```

```text {filename="Generated error"}
Property 'button' does not exist on type 'Event'.
```

Based on the fact that the above function is being assigned to `Window.onscroll`, TypeScript knows that `uiEvent` is a [UIEvent](https://developer.mozilla.org/docs/Web/API/UIEvent), and not a [MouseEvent](https://developer.mozilla.org/docs/Web/API/MouseEvent) like the previous example. `UIEvent` objects contain no `button` property, and so TypeScript will throw an error.

If this function were not in a contextually typed position, the function’s argument would implicitly have type `any`, and no error would be issued (unless you are using the [`noImplicitAny` ↗](https://www.typescriptlang.org/tsconfig.html#noImplicitAny) option):

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEDsHsEkFsAOAbAlgY1QFwIKQJ4BcoAZgIbIDOApgFDrSSVagAWZkAJstQE6gBeUgFdI6LKkagAFMNQBRAG7VIWAJSgA3rVCgGTaDwB0yaAHNZC5aqMAjYViyM1AblAhQAHgC0oAPIA0rQAvi5AA)

```ts
const handler = function (uiEvent) {
  console.log(uiEvent.button); // <- OK
};
```

We can also explicitly give type information to the function’s argument to override any contextual type:

[Try this code ↗](https://www.typescriptlang.org/play/#code/O4SwdgJg9sB0VgM4GMBOUA2GAEBebAZgK5jIAuIC2AFESAKIBuApmGQFzYCGYAngJTYA3gChs2ZAkSZmsDFADmtBizawARkTJkE-ANzYA9IewAeALTYAcjAA02MFGzNU6VNhCJsCkKpEBfPSA)

```ts
window.onscroll = function (uiEvent: any) {
  console.log(uiEvent.button); // <- Now, no error is given
};
```

However, this code will log `undefined`, since `uiEvent` has no property called `button`.

Contextual typing applies in many cases.
Common cases include arguments to function calls, right hand sides of assignments, type assertions, members of object and array literals, and return statements.
The contextual type also acts as a candidate type in best common type. For example:

[Try this code ↗](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgCCAdvALY6gDeAvkSWaAEoAW8VA9qDwAPaHioATctToNGBUKG6kAEv1hU0cAK54A3AVbFS5AKLY8AB2VVoQ0eKmUa9bEwVLSAFVjaqAay1ffUM2E1AAZSpMfzx7MUlpFzkPZUgAGTwAc0g0LFwQ1hBQAFoyxG1oMpKCdD9keH4qUERYPEwxAC1+fgAKAEo0GVcAbQBdd0U26G0NUBGqPAB3Ll4BAYAaUEWV8ysbaE3t5cjo2IGxg2YgA)

```ts
function createZoo(): Animal[] {
  return [new Rhino(), new Elephant(), new Snake()];
}
```

In this example, best common type has a set of four candidates: `Animal`, `Rhino`, `Elephant`, and `Snake`.
Of these, `Animal` can be chosen by the best common type algorithm.
