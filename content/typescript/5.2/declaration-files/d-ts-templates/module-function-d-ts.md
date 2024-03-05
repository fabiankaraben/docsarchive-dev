---
linkTitle: "Module: Function"
title: "TypeScript: Documentation - Module: Function"
description: ""
weight: 4
type: docs
---

# Module: Function

#export--and-import--require

// to understand common workarounds for this limitation of ES6 modules.

/*~ If this module is a UMD module that exposes a global variable 'myFuncLib' when
 *~ loaded outside a module loader environment, declare that global here.
 *~ Otherwise, delete this declaration.
 */
exportasnamespacemyFuncLib;

/*~ This declaration specifies that the function
 *~ is the exported object from the file
 */
export = Greeter;

/*~ This example shows how to have multiple overloads for your function */
declarefunctionGreeter(name: string): Greeter.NamedReturnType;
declarefunctionGreeter(length: number): Greeter.LengthReturnType;

/*~ If you want to expose types from your module as well, you can
 *~ place them in this block. Often you will want to describe the
 *~ shape of the return type of the function; that type should
 *~ be declared in here, as this example shows.
 *~
 *~ Note that if you decide to include this namespace, the module can be
 *~ incorrectly imported as a namespace object, unless
 *~ --esModuleInterop is turned on:
 *~   import * as x from '[~THE MODULE~]'; // WRONG! DO NOT DO THIS!
 */
declarenamespaceGreeter {
exportinterfaceLengthReturnType {
width: number;
height: number;
  }
exportinterfaceNamedReturnType {
firstName: string;
lastName: string;
  }

/*~ If the module also has properties, declare them here. For example,
   *~ this declaration says that this code is legal:
   *~   import f = require('super-greeter');
   *~   console.log(f.defaultName);
   */
exportconstdefaultName: string;
exportletdefaultLength: number;
}
```
