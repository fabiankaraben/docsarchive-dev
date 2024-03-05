---
linkTitle: "TypeScript 1.6"
title: "TypeScript: Documentation - TypeScript 1.6"
description: "TypeScript 1.6 Release Notes"
weight: 35
type: docs
canonical: /typescript/5.2/whats-new/typescript-1-6
---

# TypeScript 1.6

## JSX support {#jsx-support}

JSX is an embeddable XML-like syntax.
It is meant to be transformed into valid JavaScript, but the semantics of that transformation are implementation-specific.
JSX came to popularity with the React library but has since seen other applications.
TypeScript 1.6 supports embedding, type checking, and optionally compiling JSX directly into JavaScript.

#### New `.tsx` file extension and `as` operator {#new-tsx-file-extension-and-as-operator}

TypeScript 1.6 introduces a new `.tsx` file extension.
This extension does two things: it enables JSX inside of TypeScript files, and it makes the new `as` operator the default way to cast (removing any ambiguity between JSX expressions and the TypeScript prefix cast operator).
For example:

```ts
var x = <any>foo;
// is equivalent to:
varx = fooasany;
```

#### Using React {#using-react}

To use JSX-support with React you should use the [React typings ↗](https://github.com/borisyankov/DefinitelyTyped/tree/master/react). These typings define the `JSX` namespace so that TypeScript can correctly check JSX expressions for React. For example:

```ts
/// <reference path="react.d.ts" />

interfaceProps {
name: string;
}

classMyComponentextendsReact.Component<Props, {}> {
render() {
return <span>{this.props.name}</span>;
  }
}

<MyComponentname="bar" />; // OK
<MyComponentname={0} />; // error, `name` is not a number
```

#### Using other JSX frameworks {#using-other-jsx-frameworks}

JSX element names and properties are validated against the `JSX` namespace.
Please see the [[JSX]] wiki page for defining the `JSX` namespace for your framework.

#### Output generation {#output-generation}

TypeScript ships with two JSX modes: `preserve` and `react`.

- The `preserve` mode will keep JSX expressions as part of the output to be further consumed by another transform step. *Additionally the output will have a `.jsx` file extension.*
- The `react` mode will emit `React.createElement`, does not need to go through a JSX transformation before use, and the output will have a `.js` file extension.

See the [[JSX]] wiki page for more information on using JSX in TypeScript.

## Intersection types {#intersection-types}

TypeScript 1.6 introduces intersection types, the logical complement of union types.
A union type `A | B` represents an entity that is either of type `A` or type `B`, whereas an intersection type `A & B` represents an entity that is both of type `A`*and* type `B`.

##### Example {#example}

```ts
function extend<T, U>(first: T, second: U): T & U {
letresult = <T & U>{};
for (letidinfirst) {
result[id] = first[id];
  }
for (letidinsecond) {
if (!result.hasOwnProperty(id)) {
result[id] = second[id];
    }
  }
returnresult;
}

varx = extend({ a:"hello" }, { b:42 });
vars = x.a;
varn = x.b;
```

```ts
type LinkedList<T> = T & { next: LinkedList<T> };

interfacePerson {
name: string;
}

varpeople: LinkedList<Person>;
vars = people.name;
vars = people.next.name;
vars = people.next.next.name;
vars = people.next.next.next.name;
```

```ts
interface A {
a: string;
}
interfaceB {
b: string;
}
interfaceC {
c: string;
}

varabc: A & B & C;
abc.a = "hello";
abc.b = "hello";
abc.c = "hello";
```

See [issue #1256 ↗](https://github.com/Microsoft/TypeScript/issues/1256) for more information.

## Local type declarations {#local-type-declarations}

Local class, interface, enum, and type alias declarations can now appear inside function declarations. Local types are block scoped, similar to variables declared with `let` and `const`. For example:

```ts
function f() {
if (true) {
interfaceT {
x: number;
    }
letv: T;
v.x = 5;
  } else {
interfaceT {
x: string;
    }
letv: T;
v.x = "hello";
  }
}
```

The inferred return type of a function may be a type declared locally within the function. It is not possible for callers of the function to reference such a local type, but it can of course be matched structurally. For example:

```ts
interface Point {
x: number;
y: number;
}

functiongetPointFactory(x: number, y: number) {
classP {
x = x;
y = y;
  }
returnP;
}

varPointZero = getPointFactory(0, 0);
varPointOne = getPointFactory(1, 1);
varp1 = newPointZero();
varp2 = newPointZero();
varp3 = newPointOne();
```

Local types may reference enclosing type parameters and local class and interfaces may themselves be generic. For example:

```ts
function f3() {
functionf<X, Y>(x: X, y: Y) {
classC {
publicx = x;
publicy = y;
    }
returnC;
  }
letC = f(10, "hello");
letv = newC();
letx = v.x; // number
lety = v.y; // string
}
```

## Class expressions {#class-expressions}

TypeScript 1.6 adds support for ES6 class expressions. In a class expression, the class name is optional and, if specified, is only in scope in the class expression itself. This is similar to the optional name of a function expression. It is not possible to refer to the class instance type of a class expression outside the class expression, but the type can of course be matched structurally. For example:

```ts
let Point = class {
constructor(publicx: number, publicy: number) {}
publiclength() {
returnMath.sqrt(this.x * this.x + this.y * this.y);
  }
};
varp = newPoint(3, 4); // p has anonymous class type
console.log(p.length());
```

## Extending expressions {#extending-expressions}

TypeScript 1.6 adds support for classes extending arbitrary expression that computes a constructor function. This means that built-in types can now be extended in class declarations.

The `extends` clause of a class previously required a type reference to be specified. It now accepts an expression optionally followed by a type argument list. The type of the expression must be a constructor function type with at least one construct signature that has the same number of type parameters as the number of type arguments specified in the `extends` clause. The return type of the matching construct signature(s) is the base type from which the class instance type inherits. Effectively, this allows both real classes and “class-like” expressions to be specified in the `extends` clause.

Some examples:

```ts
// Extend built-in types

classMyArrayextendsArray<number> {}
classMyErrorextendsError {}

// Extend computed base class

classThingA {
getGreeting() {
return"Hello from A";
  }
}

classThingB {
getGreeting() {
return"Hello from B";
  }
}

interfaceGreeter {
getGreeting(): string;
}

interfaceGreeterConstructor {
new (): Greeter;
}

functiongetGreeterBase(): GreeterConstructor {
returnMath.random() >= 0.5 ? ThingA : ThingB;
}

classTestextendsgetGreeterBase() {
sayHello() {
console.log(this.getGreeting());
  }
}
```

## `abstract` classes and methods {#abstract-classes-and-methods}

TypeScript 1.6 adds support for `abstract` keyword for classes and their methods. An abstract class is allowed to have methods with no implementation, and cannot be constructed.

##### Examples {#examples}

```ts
abstract class Base {
abstractgetThing(): string;
getOtherThing() {
return"hello";
  }
}

letx = newBase(); // Error, 'Base' is abstract

// Error, must either be 'abstract' or implement concrete 'getThing'
classDerived1extendsBase {}

classDerived2extendsBase {
getThing() {
return"hello";
  }
foo() {
super.getThing(); // Error: cannot invoke abstract members through 'super'
  }
}

varx = newDerived2(); // OK
vary: Base = newDerived2(); // Also OK
y.getThing(); // OK
y.getOtherThing(); // OK
```

## Generic type aliases {#generic-type-aliases}

With TypeScript 1.6, type aliases can be generic. For example:

```ts
type Lazy<T> = T | (() => T);

vars: Lazy<string>;
s = "eager";
s = () =>"lazy";

interfaceTuple<A, B> {
a: A;
b: B;
}

typePair<T> = Tuple<T, T>;
```

## Stricter object literal assignment checks {#stricter-object-literal-assignment-checks}

TypeScript 1.6 enforces stricter object literal assignment checks for the purpose of catching excess or misspelled properties. Specifically, when a fresh object literal is assigned to a variable or passed as an argument for a non-empty target type, it is an error for the object literal to specify properties that don’t exist in the target type.

##### Examples {#examples-1}

```ts
var x: { foo: number };
x = { foo:1, baz:2 }; // Error, excess property `baz`

vary: { foo: number; bar?: number };
y = { foo:1, baz:2 }; // Error, excess or misspelled property `baz`
```

A type can include an index signature to explicitly indicate that excess properties are permitted:

```ts
var x: { foo: number; [x: string]: any };
x = { foo:1, baz:2 }; // Ok, `baz` matched by index signature
```

## ES6 generators {#es6-generators}

TypeScript 1.6 adds support for generators when targeting ES6.

A generator function can have a return type annotation, just like a function. The annotation represents the type of the generator returned by the function. Here is an example:

```ts
function* g(): Iterable<string> {
for (vari = 0; i < 100; i++) {
yield""; // string is assignable to string
  }
yield*otherStringGenerator(); // otherStringGenerator must be iterable and element type assignable to string
}
```

A generator function with no type annotation can have the type annotation inferred.
So in the following case, the type will be inferred from the yield statements:

```ts
function* g() {
for (vari = 0; i < 100; i++) {
yield""; // infer string
  }
yield*otherStringGenerator(); // infer element type of otherStringGenerator
}
```

## Experimental support for `async` functions {#experimental-support-for-async-functions}

TypeScript 1.6 introduces experimental support of `async` functions when targeting ES6.
Async functions are expected to invoke an asynchronous operation and await its result without blocking normal execution of the program.
This accomplished through the use of an ES6-compatible `Promise` implementation, and transposition of the function body into a compatible form to resume execution when the awaited asynchronous operation completes.

An *async function* is a function or method that has been prefixed with the `async` modifier. This modifier informs the compiler that function body transposition is required, and that the keyword `await` should be treated as a unary expression instead of an identifier.
An *Async Function* must provide a return type annotation that points to a compatible `Promise` type. Return type inference can only be used if there is a globally defined, compatible `Promise` type.

##### Example {#example-1}

```ts
var p: Promise<number> = /* ... */;
asyncfunctionfn(): Promise<number> {
vari = awaitp; // suspend execution until 'p' is settled. 'i' has type "number"
return1 + i;
}

vara = async (): Promise<number> =>1 + awaitp; // suspends execution.
vara = async () =>1 + awaitp; // suspends execution. return type is inferred as "Promise<number>" when compiling with --target ES6
varfe = asyncfunction(): Promise<number> {
vari = awaitp; // suspend execution until 'p' is settled. 'i' has type "number"
return1 + i;
}

classC {
asyncm(): Promise<number> {
vari = awaitp; // suspend execution until 'p' is settled. 'i' has type "number"
return1 + i;
  }

asyncgetp(): Promise<number> {
vari = awaitp; // suspend execution until 'p' is settled. 'i' has type "number"
return1 + i;
  }
}
```

## Nightly builds {#nightly-builds}

While not strictly a language change, nightly builds are now available by installing with the following command:

```
npm install -g typescript@next
```

## Adjustments in module resolution logic {#adjustments-in-module-resolution-logic}

Starting from release 1.6 TypeScript compiler will use different set of rules to resolve module names when targeting ‘commonjs’.
These [rules ↗](https://github.com/Microsoft/TypeScript/issues/2338) attempted to model module lookup procedure used by Node.
This effectively mean that node modules can include information about its typings and TypeScript compiler will be able to find it.
User however can override module resolution rules picked by the compiler by using [`moduleResolution` ↗](https://www.typescriptlang.org/tsconfig.html#moduleResolution) command line option. Possible values are:

- ‘classic’ - module resolution rules used by pre 1.6 TypeScript compiler
- ‘node’ - node-like module resolution

## Merging ambient class and interface declaration {#merging-ambient-class-and-interface-declaration}

The instance side of an ambient class declaration can be extended using an interface declaration The class constructor object is unmodified.
For example:

```ts
declare class Foo {
publicx: number;
}

interfaceFoo {
y: string;
}

functionbar(foo: Foo) {
foo.x = 1; // OK, declared in the class Foo
foo.y = "1"; // OK, declared in the interface Foo
}
```

## User-defined type guard functions {#user-defined-type-guard-functions}

TypeScript 1.6 adds a new way to narrow a variable type inside an `if` block, in addition to `typeof` and `instanceof`.
A user-defined type guard functions is one with a return type annotation of the form `x is T`, where `x` is a declared parameter in the signature, and `T` is any type.
When a user-defined type guard function is invoked on a variable in an `if` block, the type of the variable will be narrowed to `T`.

##### Examples {#examples-2}

```ts
function isCat(a: any): a is Cat {
returna.name === "kitty";
}

varx: Cat | Dog;
if (isCat(x)) {
x.meow(); // OK, x is Cat in this block
}
```

## `exclude` property support in tsconfig.json {#exclude-property-support-in-tsconfigjson}

A tsconfig.json file that doesn’t specify a files property (and therefore implicitly references all *.ts files in all subdirectories) can now contain an exclude property that specifies a list of files and/or directories to exclude from the compilation.
The exclude property must be an array of strings that each specify a file or folder name relative to the location of the tsconfig.json file.
For example:

```
{
"[compilerOptions ↗](https://www.typescriptlang.org/tsconfig.html#compilerOptions)": {
"[out ↗](https://www.typescriptlang.org/tsconfig.html#out)": "test.js"
  },
"[exclude ↗](https://www.typescriptlang.org/tsconfig.html#exclude)": ["node_modules", "test.ts", "utils/t2.ts"]
}
```

The [`exclude` ↗](https://www.typescriptlang.org/tsconfig.html#exclude) list does not support wildcards. It must simply be a list of files and/or directories.

## `--init` command line option {#--init-command-line-option}

Run `tsc --init` in a directory to create an initial `tsconfig.json` in this directory with preset defaults.
Optionally pass command line arguments along with `--init` to be stored in your initial tsconfig.json on creation.
