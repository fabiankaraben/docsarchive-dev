---
linkTitle: "Module: Class"
title: "TypeScript: Documentation - Module: Class"
description: ""
weight: 3
type: docs
---

# Module: Class

#export--and-import--require

// to understand common workarounds for this limitation of ES6 modules.

/*~ If this module is a UMD module that exposes a global variable 'myClassLib' when
 *~ loaded outside a module loader environment, declare that global here.
 *~ Otherwise, delete this declaration.
 */
exportasnamespace"super-greeter";

/*~ This declaration specifies that the class constructor function
 *~ is the exported object from the file
 */
export = Greeter;

/*~ Write your module's methods and properties in this class */
declareclassGreeter {
constructor(customGreeting?: string);

greet: void;

myMethod(opts: MyClass.MyClassMethodOptions): number;
}

/*~ If you want to expose types from your module as well, you can
 *~ place them in this block.
 *~
 *~ Note that if you decide to include this namespace, the module can be
 *~ incorrectly imported as a namespace object, unless
 *~ --esModuleInterop is turned on:
 *~   import * as x from '[~THE MODULE~]'; // WRONG! DO NOT DO THIS!
 */
declarenamespaceMyClass {
exportinterfaceMyClassMethodOptions {
width?: number;
height?: number;
  }
}
```
