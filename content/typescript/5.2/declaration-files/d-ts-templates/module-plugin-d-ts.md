---
linkTitle: "Module: Plugin"
title: "TypeScript: Documentation - Module: Plugin"
description: ""
weight: 2
type: docs
---

# Module: Plugin

For example, when you want to work with JavaScript code which extends another library.

```ts
import { greeter } from "super-greeter";

// Normal Greeter API
greeter(2);
greeter("Hello world");

// Now we extend the object with a new function at runtime
import"hyper-super-greeter";
greeter.hyperGreet();
```

The definition for “super-greeter”:

```ts
/*~ This example shows how to have multiple overloads for your function */
exportinterfaceGreeterFunction {
  (name: string): void
  (time: number): void
}

/*~ This example shows how to export a function specified by an interface */
exportconstgreeter: GreeterFunction;
```

We can extend the existing module like the following:

```ts
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>

/*~ This is the module plugin template file. You should rename it to index.d.ts
 *~ and place it in a folder with the same name as the module.
 *~ For example, if you were writing a file for "super-greeter", this
 *~ file should be 'super-greeter/index.d.ts'
 */

/*~ On this line, import the module which this module adds to */
import { greeter } from"super-greeter";

/*~ Here, declare the same module as the one you imported above
 *~ then we expand the existing declaration of the greeter function
 */
exportmodule"super-greeter" {
exportinterfaceGreeterFunction {
/** Greets even better! */
hyperGreet(): void;
  }
}
```

This uses [declaration merging](/typescript/5.2/reference/declaration-merging)

## The Impact of ES6 on Module Plugins {#the-impact-of-es6-on-module-plugins}

Some plugins add or modify top-level exports on existing modules.
While this is legal in CommonJS and other loaders, ES6 modules are considered immutable and this pattern will not be possible.
Because TypeScript is loader-agnostic, there is no compile-time enforcement of this policy, but developers intending to transition to an ES6 module loader should be aware of this.
