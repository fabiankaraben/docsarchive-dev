---
linkTitle: "Global: Modifying Module"
title: "TypeScript: Documentation - Global: Modifying Module"
description: ""
weight: 6
type: docs
next: /typescript/5.2/declaration-files/do-s-and-don-ts
---

# Global: Modifying Module

## *Global-modifying Modules* {#global-modifying-modules}

A *global-modifying module* alters existing values in the global scope when they are imported.
For example, there might exist a library which adds new members to `String.prototype` when imported.
This pattern is somewhat dangerous due to the possibility of runtime conflicts,
but we can still write a declaration file for it.

## Identifying global-modifying modules {#identifying-global-modifying-modules}

Global-modifying modules are generally easy to identify from their documentation.
In general, they’re similar to global plugins, but need a `require` call to activate their effects.

You might see documentation like this:

```js
// 'require' call that doesn't use its return value
varunused = require("magic-string-time");
/* or */
require("magic-string-time");

varx = "hello, world";
// Creates new methods on built-in types
console.log(x.startsWithHello());

vary = [1, 2, 3];
// Creates new methods on built-in types
console.log(y.reverseAndSort());
```

Here is an example

```ts
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>

/*~ This is the global-modifying module template file. You should rename it to index.d.ts
 *~ and place it in a folder with the same name as the module.
 *~ For example, if you were writing a file for "super-greeter", this
 *~ file should be 'super-greeter/index.d.ts'
 */

/*~ Note: If your global-modifying module is callable or constructable, you'll
 *~ need to combine the patterns here with those in the module-class or module-function
 *~ template files
 */
declareglobal {
/*~ Here, declare things that go in the global namespace, or augment
   *~ existing declarations in the global namespace
   */
interfaceString {
fancyFormat(opts: StringFormatOptions): string;
  }
}

/*~ If your module exports types or values, write them as usual */
exportinterfaceStringFormatOptions {
fancinessLevel: number;
}

/*~ For example, declaring a method on the module (in addition to its global side effects) */
exportfunctiondoSomething(): void;

/*~ If your module exports nothing, you'll need this line. Otherwise, delete it */
export {};
```
