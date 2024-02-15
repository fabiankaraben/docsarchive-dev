---
linkTitle: "Type Checking JavaScript Files"
title: "TypeScript: Documentation - Type Checking JavaScript Files"
description: "How to add type checking to JavaScript files using TypeScript"
weight: 2
type: docs
---

# Type Checking JavaScript Files

Here are some notable differences on how checking works in `.js` files compared to `.ts` files.

## Properties are inferred from assignments in class bodies {#properties-are-inferred-from-assignments-in-class-bodies}

ES2015 does not have a means for declaring properties on classes. Properties are dynamically assigned, just like object literals.

In a `.js` file, the compiler infers properties from property assignments inside the class body.
The type of a property is the type given in the constructor, unless it’s not defined there, or the type in the constructor is undefined or null.
In that case, the type is the union of the types of all the right-hand values in these assignments.
Properties defined in the constructor are always assumed to exist, whereas ones defined just in methods, getters, or setters are considered optional.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEFYCdsHtsUAuUAJgGZTS1IAbAQxRVAGFQBvNUUSPAOxQAXbAFdIgggAoAlBy7dQg6AEsUAOl4DhYidgDyfWgE9QAXlAAGANzzuS1Rv5DR4ggFU+CPngDufM6AifAAmsABmynywwTbcAL7yALawSnjBMnIKiirqms46BAbGAWH0tCiwsQr2uU7artgeXr7+5gBEAA60QQgARvSCbVagmHgIADQ8dS66Td5+oKqgzpEA5qAAPoEh4ZHRttkOyanBRSbtY0MjYGOTvSKCoMfQaWdTIrTBoGUoeKC9sG2oQiUWC8gS3GeaVIGU4WRqaihp0M50Uokq12+5Twk1uTxSL2RxgA5MxBEYOoClis+Ostr08HhaLB6P4tkFgXswfE0HEgA)

```js
class C {
  constructor() {
    this.constructorOnly = 0;
    this.constructorUnknown = undefined;
  }
  method() {
    this.constructorOnly = false;
    this.constructorUnknown = "plunkbat"; // ok, constructorUnknown is string | undefined
    this.methodOnly = "ok"; // ok, but methodOnly could also be undefined
  }
  method2() {
    this.methodOnly = true; // also, ok, methodOnly's type is string | boolean | undefined
  }
}
```

```text {filename="Generated error"}
Type 'boolean' is not assignable to type 'number'.
```

If properties are never set in the class body, they are considered unknown.
If your class has properties that are only read from, add and then annotate a declaration in the constructor with JSDoc to specify the type.
You don’t even have to give a value if it will be initialized later:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEFYCdsHtsUAuUAJgGZTS1IAbAQxRVAGFQBvNUUSPAOxQAXbAFdIgggAoAlBy7dQwAFRKIggJ4AHWBz4iAtgCMcoAD6gRfACawAZgEs+sKwF9QS4PO6Do9lADpNfE1QAF4LaztHZwBuL0UVNS0ddj0jE3NLGwcnV3dPBVAfP39eS0E47hc0arRaWEEeMNAnAHdWGTjIQODmgAYYxTAAeQBpGlK8cuaAIiFsRwBzGZigA)

```js
class C {
  constructor() {
    /** @type {number | undefined} */
    this.prop = undefined;
    /** @type {number | undefined} */
    this.count;
  }
}
 
let c = new C();
c.prop = 0; // OK
c.count = "string";
```

```text {filename="Generated error"}
Type 'string' is not assignable to type 'number'.
```

## Constructor functions are equivalent to classes {#constructor-functions-are-equivalent-to-classes}

Before ES2015, JavaScript used constructor functions instead of classes.
The compiler supports this pattern and understands constructor functions as equivalent to ES2015 classes.
The property inference rules described above work exactly the same way.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEFYCdsHtsUAuUAJgDYAOAZjOtNLQDMBXAO0gBcBLPN0AMIAKAJSgA3mlChO0bigB0kPik7YWXAgHk2AGwCeoALygADAG4pMuYuVtV6zdgCqbBGzwB3fifYATWCZuNlg-SwBfNAEFAAd8TjxOfRjYBQBbWFk8P2NQVg4ePlBRCStZeSUVNQ0E7B0DXKYAQ10UWEtpctsqx1rXdy8fUAAiGN12BAAjJs5h81BMLQBpABprWBlkjflQB2CAc1AAH1B-QODQtHDzIA)

```js
function C() {
  this.constructorOnly = 0;
  this.constructorUnknown = undefined;
}
C.prototype.method = function () {
  this.constructorOnly = false;
  this.constructorUnknown = "plunkbat"; // OK, the type is string | undefined
};
```

```text {filename="Generated error"}
Type 'boolean' is not assignable to type 'number'.
```

## CommonJS modules are supported {#commonjs-modules-are-supported}

In a `.js` file, TypeScript understands the CommonJS module format.
Assignments to `exports` and `module.exports` are recognized as export declarations.
Similarly, `require` function calls are recognized as module imports. For example:

```js
// same as `import module "fs"`
const fs = require("fs");

// same as `export function readFile`
module.exports.readFile = function (f) {
  return fs.readFileSync(f);
};
```

The module support in JavaScript is much more syntactically forgiving than TypeScript’s module support.
Most combinations of assignments and declarations are supported.

## Classes, functions, and object literals are namespaces {#classes-functions-and-object-literals-are-namespaces}

Classes are namespaces in `.js` files.
This can be used to nest classes, for example:

[Try this code ↗](https://www.typescriptlang.org/play#code/MYGwhgzhAEDC0G8C+AoWA6AItAvNUkMyA3EA)

```js
class C {}
C.D = class {};
```

And, for pre-ES2015 code, it can be used to simulate static methods:

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABAeRFApgJwBQEpEDeAUIolABYwDOAdAJ6IC8iATANxEC+RRqGmNAJJgwWJolCRYCRHkIkylWnQbN2XDrzRYhIrHjZA)

```js
function Outer() {
  this.y = 2;
}
 
Outer.Inner = function () {
  this.yy = 2;
};
 
Outer.Inner();
```

It can also be used to create simple namespaces:

[Try this code ↗](https://www.typescriptlang.org/play#code/G4QwTgBAdgzhC8EDeBfA3AKFgOgMIIgGMAbEGOVTHAMwFcpCC6GAXASwHsoIAKASmToMWGGiA)

```js
var ns = {};
ns.C = class {};
ns.func = function () {};
 
ns;
```

Other variants are allowed as well:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEElwMQUQKAG4EMBOoB2BnUBeUAKAMwFd0BjAFwEsB7dA9ASlAG85RQUBTC4le+gB9BrAL4BuOKMb5GkrADoAwgHkAcgGUAKrlABGSXBCgAJl0JJiAG2roA5qAo1QdqzQBGSK4lSgkmTCo7ehx2PwCgoUEwknJqOgJmNg4OYzIaMxcaLmwACy5uMIk4f0DghS4ADwoUJF0DIA)

```js
// IIFE
var ns = (function (n) {
  return n || {};
})();
ns.CONST = 1;
 
// defaulting to global
var assign =
  assign ||
  function () {
    // code goes here
  };
assign.extra = 1;
```

## Object literals are open-ended {#object-literals-are-open-ended}

In a `.ts` file, an object literal that initializes a variable declaration gives its type to the declaration.
No new members can be added that were not specified in the original literal.
This rule is relaxed in a `.js` file; object literals have an open-ended type (an index signature) that allows adding and looking up properties that were not defined originally.
For instance:

[Try this code ↗](https://www.typescriptlang.org/play#code/G4QwTgBA9gRgVhAvBA3hEAuCBGCBfAbgChY4A6GJCAJgIgHp6IBBAG1agHcBTAEyA)

```js
var obj = { a: 1 };
obj.b = 2; // Allowed
```

Object literals behave as if they have an index signature `[x:string]: any` that allows them to be treated as open maps instead of closed objects.

Like other special JS checking behaviors, this behavior can be changed by specifying a JSDoc type for the variable. For example:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEFYCdsHtsUAuUAJgGZyBODAKlogBcBPAB1lAG9OBDEgOwCuAWwBGOAL4TQtYGgBuPbKDyiAVqAC8XUH1ABGUBIDcaVWoB0orWWNA)

```js
/** @type {{a: number}} */
var obj = { a: 1 };
obj.b = 2;
```

```text {filename="Generated error"}
Property 'b' does not exist on type '{ a: number; }'.
```

## null, undefined, and empty array initializers are of type any or any[] {#null-undefined-and-empty-array-initializers-are-of-type-any-or-any}

Any variable, parameter or property that is initialized with null or undefined will have type any, even if strict null checks is turned on.
Any variable, parameter or property that is initialized with [] will have type any[], even if strict null checks is turned on.
The only exception is for properties that have multiple initializers as described above.

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABAMTnAFDRBeRYQA2BAlIgN4BQiiMwi6AhDKVrgIwDcViAbgIYAnRACscicABMApsBhgpErtVG4ATEsRQAFjADOAOgJiA2gF0uAXwoV+Q4GjHyA7ijTpiXe3EP6ADiF0tdC99Zk80H39A9AAiKTAJGI8gA)

```js
function Foo(i = null) {
  if (!i) i = 1;
  var j = undefined;
  j = 2;
  this.l = [];
}
 
var foo = new Foo();
foo.l.push(foo.i);
foo.l.push("end");
```

## Function parameters are optional by default {#function-parameters-are-optional-by-default}

Since there is no way to specify optionality on parameters in pre-ES2015 JavaScript, all function parameters in `.js` file are considered optional.
Calls with fewer arguments than the declared number of parameters are allowed.

It is important to note that it is an error to call a function with too many arguments.

For instance:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGMAsFNINYCkDOAoEEUBcBOBLSbALlADMBDAGxVgzHFl1wHtcVSB2ABm4DZQPfqABMAVjEAWNGQCuAOyL4W80ACMKuABQUANOoCUoAN5pQoSCpQsqsAHRUWAcx2gA1KABEX94YDcaAC+aGga2gCMBn6gmADyANL6tJbyACagmk6yALaw8tgWVvipTLDpLAAO2Mry1KGaWuH6IlH1Ec36AMxRMWAAosxs+tgsLKDZFPIAnhm4Wbn5KEA)

```js
function bar(a, b) {
  console.log(a + " " + b);
}
 
bar(1); // OK, second argument considered optional
bar(1, 2);
bar(1, 2, 3); // Error, too many arguments
```

```text {filename="Generated error"}
Expected 0-2 arguments, but got 3.
```

JSDoc annotated functions are excluded from this rule.
Use JSDoc optional parameter syntax (`[``]`) to express optionality. e.g.:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhCgAIUgBAHAhgJ0QW0gbwM4BdkCWAdgOYC+kA2tgPZoCmARjQCYCeAupALSQDKdJqzYBybJCLp6AOighg4AGYBXIgGNcBGkUjZEbABL0ANsZoAKWg2bsAlFiiQCiyOYCEVoXYeRfuwTZskAC8kABEAFI0ABY6ACI09GEA3I5kjmratMYyZiTmYUamNOGQANT+1sK2qengeoYmZuY1QA)

```js
/**
 * @param {string} [somebody] - Somebody's name.
 */
function sayHello(somebody) {
  if (!somebody) {
    somebody = "John Doe";
  }
  console.log("Hello " + somebody);
}
 
sayHello();
```

## Var-args parameter declaration inferred from use of `arguments` {#var-args-parameter-declaration-inferred-from-use-of-arguments}

A function whose body has a reference to the `arguments` reference is implicitly considered to have a var-arg parameter (i.e. `(...arg: any[]) => any`). Use JSDoc var-arg syntax to specify the type of the arguments.

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEAcEMCdYFtwG8B0mB2BXJAjAU3gF9wEBzAZ3BGACgAzHLAYwBcBLAey3CrwAKUOFwFiNOgEo09cOABuCcO27tYAG3ABecAAYA3HPCNu8cIKXnOO-QfA2APOXgU8hLOyroNHiuwALe04AahCZVGN5VXUtEN1Kd08qAG1OAF0jeRJjeEJ2HHg+GM0jEiA)

```js
/** @param {...number} args */
function sum(/* numbers */) {
  var total = 0;
  for (var i = 0; i < arguments.length; i++) {
    total += arguments[i];
  }
  return total;
}
```

## Unspecified type parameters default to `any` {#unspecified-type-parameters-default-to-any}

Since there is no natural syntax for specifying generic type parameters in JavaScript, an unspecified type parameter defaults to `any`.

### In extends clause {#in-extends-clause}

For instance, `React.Component` is defined to have two type parameters, `Props` and `State`.
In a `.js` file, there is no legal way to specify these in the extends clause. By default the type arguments will be `any`:

```js
import { Component } from "react";

class MyComponent extends Component {
  render() {
    this.props.b; // Allowed, since this.props is of type any
  }
}
```

Use JSDoc `@augments` to specify the types explicitly. for instance:

```js
import { Component } from "react";

/**
 * @augments {Component<{a: number}, State>}
 */
class MyComponent extends Component {
  render() {
    this.props.b; // Error: b does not exist on {a:number}
  }
}
```

### In JSDoc references {#in-jsdoc-references}

An unspecified type argument in JSDoc defaults to any:

[Try this code ↗](https://www.typescriptlang.org/play#code/PQKhAIAEBcE8AcCmBvAggJ3QQ1gX3CMAFABuW64AHuALzgDaAugNxFGUB08ArgM4AWACgCMASmbhgwcAHkA0uy58hAIl7R0ASwB2AcxXjJ0+QBoq4Tb3AB7AGbg4ScBmywAPFm2wAfG1AQYBBQXHA43bW4AWwAjRHRvfEJScnBYWgYWNlglARFDKVkFbJ5ctQ0dfXzpAFFMa3QzdS09Cytta2hwLF5eTV1tLGiAG0QHa3AImLigA)

```js
/** @type{Array} */
var x = [];
 
x.push(1); // OK
x.push("string"); // OK, x is of type Array<any>
 
/** @type{Array.<number>} */
var y = [];
 
y.push(1); // OK
y.push("string"); // Error, string is not assignable to number
```

### In function calls {#in-function-calls}

A call to a generic function uses the arguments to infer the type parameters. Sometimes this process fails to infer any types, mainly because of lack of inference sources; in these cases, the type parameters will default to `any`. For example:

```js
var p = new Promise((resolve, reject) => {
  reject();
});

p; // Promise<any>;
```

To learn all of the features available in JSDoc, see [the reference](/typescript/5.1/javascript/jsdoc-supported-types).
