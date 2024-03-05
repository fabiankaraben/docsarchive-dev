---
linkTitle: "Declaration Reference"
title: "TypeScript: Documentation - Declaration Reference"
description: "How to create a d.ts file for a module"
weight: 2
type: docs
---

# Declaration Reference

## Objects with Properties {#objects-with-properties}

*Documentation*

> The global variable `myLib` has a function `makeGreeting` for creating greetings,
> and a property `numberOfGreetings` indicating the number of greetings made so far.
> 

*Code*

```ts
let result = myLib.makeGreeting("hello, world");
console.log("The computed greeting is:" + result);

letcount = myLib.numberOfGreetings;
```

*Declaration*

Use `declare namespace` to describe types or values accessed by dotted notation.

```ts
declare namespace myLib {
functionmakeGreeting(s: string): string;
letnumberOfGreetings: number;
}
```

## Overloaded Functions {#overloaded-functions}

*Documentation*

The `getWidget` function accepts a number and returns a Widget, or accepts a string and returns a Widget array.

*Code*

```ts
let x: Widget = getWidget(43);

letarr: Widget[] = getWidget("all of them");
```

*Declaration*

```ts
declare function getWidget(n: number): Widget;
declarefunctiongetWidget(s: string): Widget[];
```

## Reusable Types (Interfaces) {#reusable-types-interfaces}

*Documentation*

> When specifying a greeting, you must pass a `GreetingSettings` object.
> This object has the following properties:
> 1 - greeting: Mandatory string
> 2 - duration: Optional length of time (in milliseconds)
> 3 - color: Optional string, e.g. ‘#ff00ff’
> 

*Code*

```ts
greet({
greeting:"hello world",
duration:4000
});
```

*Declaration*

Use an `interface` to define a type with properties.

```ts
interface GreetingSettings {
greeting: string;
duration?: number;
color?: string;
}

declarefunctiongreet(setting: GreetingSettings): void;
```

## Reusable Types (Type Aliases) {#reusable-types-type-aliases}

*Documentation*

> Anywhere a greeting is expected, you can provide a `string`, a function returning a `string`, or a `Greeter` instance.
> 

*Code*

```ts
function getGreeting() {
return"howdy";
}
classMyGreeterextendsGreeter {}

greet("hello");
greet(getGreeting);
greet(newMyGreeter());
```

*Declaration*

You can use a type alias to make a shorthand for a type:

```ts
type GreetingLike = string | (() => string) | MyGreeter;

declarefunctiongreet(g: GreetingLike): void;
```

## Organizing Types {#organizing-types}

*Documentation*

> The `greeter` object can log to a file or display an alert.
> You can provide LogOptions to `.log(...)` and alert options to `.alert(...)`
> 

*Code*

```ts
const g = new Greeter("Hello");
g.log({ verbose:true });
g.alert({ modal:false, title:"Current Greeting" });
```

*Declaration*

Use namespaces to organize types.

```ts
declare namespace GreetingLib {
interfaceLogOptions {
verbose?: boolean;
  }
interfaceAlertOptions {
modal: boolean;
title?: string;
color?: string;
  }
}
```

You can also create nested namespaces in one declaration:

```ts
declare namespace GreetingLib.Options {
// Refer to via GreetingLib.Options.Log
interfaceLog {
verbose?: boolean;
  }
interfaceAlert {
modal: boolean;
title?: string;
color?: string;
  }
}
```

## Classes {#classes}

*Documentation*

> You can create a greeter by instantiating the `Greeter` object, or create a customized greeter by extending from it.
> 

*Code*

```ts
const myGreeter = new Greeter("hello, world");
myGreeter.greeting = "howdy";
myGreeter.showGreeting();

classSpecialGreeterextendsGreeter {
constructor() {
super("Very special greetings");
  }
}
```

*Declaration*

Use `declare class` to describe a class or class-like object.
Classes can have properties and methods as well as a constructor.

```ts
declare class Greeter {
constructor(greeting: string);

greeting: string;
showGreeting(): void;
}
```

## Global Variables {#global-variables}

*Documentation*

> The global variable `foo` contains the number of widgets present.
> 

*Code*

```ts
console.log("Half the number of widgets is " + foo / 2);
```

*Declaration*

Use `declare var` to declare variables.
If the variable is read-only, you can use `declare const`.
You can also use `declare let` if the variable is block-scoped.

```ts
/** The number of widgets present */
declarevarfoo: number;
```

## Global Functions {#global-functions}

*Documentation*

> You can call the function `greet` with a string to show a greeting to the user.
> 

*Code*

```ts
greet("hello, world");
```

*Declaration*

Use `declare function` to declare functions.

```ts
declare function greet(greeting: string): void;
```
