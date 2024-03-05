---
linkTitle: "JSX"
title: "TypeScript: Documentation - JSX"
description: "Using JSX with TypeScript"
weight: 7
type: docs
---

# JSX

## Basic usage {#basic-usage}

In order to use JSX you must do two things.

1. Name your files with a `.tsx` extension
2. Enable the [`jsx` ↗](https://www.typescriptlang.org/tsconfig.html#jsx) option

TypeScript ships with three JSX modes: `preserve`, `react`, and `react-native`.
These modes only affect the emit stage - type checking is unaffected.
The `preserve` mode will keep the JSX as part of the output to be further consumed by another transform step (e.g. [Babel ↗](https://babeljs.io/)).
Additionally the output will have a `.jsx` file extension.
The `react` mode will emit `React.createElement`, does not need to go through a JSX transformation before use, and the output will have a `.js` file extension.
The `react-native` mode is the equivalent of `preserve` in that it keeps all JSX, but the output will instead have a `.js` file extension.

|Mode|Input|Output|Output File Extension|
|---|---|---|---|
|`preserve`|`<div />`|`<div />`|`.jsx`|
|`react`|`<div />`|`React.createElement("div")`|`.js`|
|`react-native`|`<div />`|`<div />`|`.js`|
|`react-jsx`|`<div />`|`_jsx("div", {}, void 0);`|`.js`|
|`react-jsxdev`|`<div />`|`_jsxDEV("div", {}, void 0, false, {...}, this);`|`.js`|


You can specify this mode using either the [`jsx` ↗](https://www.typescriptlang.org/tsconfig.html#jsx) command line flag or the corresponding option [`jsx` in your tsconfig.json ↗](https://www.typescriptlang.org/tsconfig.html#jsx) file.

> *Note: You can specify the JSX factory function to use when targeting react JSX emit with [`jsxFactory` ↗](https://www.typescriptlang.org/tsconfig.html#jsxFactory) option (defaults to `React.createElement`)
> 

## The `as` operator {#the-as-operator}

Recall how to write a type assertion:

```ts
const foo = <foo>bar;
```

This asserts the variable `bar` to have the type `foo`.
Since TypeScript also uses angle brackets for type assertions, combining it with JSX’s syntax would introduce certain parsing difficulties. As a result, TypeScript disallows angle bracket type assertions in `.tsx` files.

Since the above syntax cannot be used in `.tsx` files, an alternate type assertion operator should be used: `as`.
The example can easily be rewritten with the `as` operator.

```ts
const foo = bar as foo;
```

The `as` operator is available in both `.ts` and `.tsx` files, and is identical in behavior to the angle-bracket type assertion style.

## Type Checking {#type-checking}

In order to understand type checking with JSX, you must first understand the difference between intrinsic elements and value-based elements.
Given a JSX expression `<expr />`, `expr` may either refer to something intrinsic to the environment (e.g. a `div` or `span` in a DOM environment) or to a custom component that you’ve created.
This is important for two reasons:

1. For React, intrinsic elements are emitted as strings (`React.createElement("div")`), whereas a component you’ve created is not (`React.createElement(MyComponent)`).
2. The types of the attributes being passed in the JSX element should be looked up differently.
  Intrinsic element attributes should be known *intrinsically* whereas components will likely want to specify their own set of attributes.

TypeScript uses the [same convention that React does ↗](https://web.archive.org/web/20231119064733/http://facebook.github.io/react/docs/jsx-in-depth.html#html-tags-vs.-react-components) for distinguishing between these.
An intrinsic element always begins with a lowercase letter, and a value-based element always begins with an uppercase letter.

### Intrinsic elements {#intrinsic-elements}

Intrinsic elements are looked up on the special interface `JSX.IntrinsicElements`.
By default, if this interface is not specified, then anything goes and intrinsic elements will not be type checked.
However, if this interface *is* present, then the name of the intrinsic element is looked up as a property on the `JSX.IntrinsicElements` interface.
For example:

```ts
declare namespace JSX {
interfaceIntrinsicElements {
foo: any;
  }
}

<foo />; // ok
<bar />; // error
```

In the above example, `<foo />` will work fine but `<bar />` will result in an error since it has not been specified on `JSX.IntrinsicElements`.

> Note: You can also specify a catch-all string indexer on `JSX.IntrinsicElements` as follows:
> 

```ts
declare namespace JSX {
interfaceIntrinsicElements {
    [elemName: string]: any;
  }
}
```

### Value-based elements {#value-based-elements}

Value-based elements are simply looked up by identifiers that are in scope.

```ts
import MyComponent from "./myComponent";

<MyComponent />; // ok
<SomeOtherComponent />; // error
```

There are two ways to define a value-based element:

1. Function Component (FC)
2. Class Component

Because these two types of value-based elements are indistinguishable from each other in a JSX expression, first TS tries to resolve the expression as a Function Component using overload resolution. If the process succeeds, then TS finishes resolving the expression to its declaration. If the value fails to resolve as a Function Component, TS will then try to resolve it as a class component. If that fails, TS will report an error.

#### Function Component {#function-component}

As the name suggests, the component is defined as a JavaScript function where its first argument is a `props` object.
TS enforces that its return type must be assignable to `JSX.Element`.

```ts
interface FooProp {
name: string;
X: number;
Y: number;
}

declarefunctionAnotherComponent(prop: { name: string });
functionComponentFoo(prop: FooProp) {
return <AnotherComponentname={prop.name} />;
}

constButton = (prop: { value: string }, context: { color: string }) => (
  <button />
);
```

Because a Function Component is simply a JavaScript function, function overloads may be used here as well:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDsHsFECd7XgZwFABMCmBjANgIbxagC20GArniQFIDKAGqAN5qigCWkALlvADMCOErBqksvVgF92XXvyEjQASV7xuKTjjFYJvFKzkcA2igBcoFDw2QA5gF1LBSAE8A3HNmyQoALQBOJQ8AX5o3HyCwiQAwnjaANYEAEY0AApIAA6GbBw4ABaceBjEkJYMjAB0uvo8Jg6gAD6gFdXikjyesuEKUcoAEtASGdDZoFgAHnyQGIZxiSnpWTly+UNY5Uxteh1daD2RSiT0nNgjY5PTs6DzOEmpWOcrHFrYm1U1HU1WNtx2e2gBJRIDgeJxoJBQABZAjcABCwR4EIAFJkspZBsNlgBKd7bWqeIEgsEQ6GwyAInhIyCo9GgE5nHF4z68QnA0HgyEw+GIlFo0aWW73JajFC4lpbFk8IwcXyVeVoaRAA)

```ts
interface ClickableProps {
children: JSX.Element[] | JSX.Element;
}

interfaceHomePropsextendsClickableProps {
home: JSX.Element;
}

interfaceSidePropsextendsClickableProps {
side: JSX.Element | string;
}

functionMainButton(prop: HomeProps): JSX.Element;
functionMainButton(prop: SideProps): JSX.Element;
functionMainButton(prop: ClickableProps): JSX.Element {
// ...
}
```

> Note: Function Components were formerly known as Stateless Function Components (SFC). As Function Components can no longer be considered stateless in recent versions of react, the type `SFC` and its alias `StatelessComponent` were deprecated.
> 

#### Class Component {#class-component}

It is possible to define the type of a class component.
However, to do so it is best to understand two new terms: the *element class type* and the *element instance type*.

Given `<Expr />`, the *element class type* is the type of `Expr`.
So in the example above, if `MyComponent` was an ES6 class the class type would be that class’s constructor and statics.
If `MyComponent` was a factory function, the class type would be that function.

Once the class type is established, the instance type is determined by the union of the return types of the class type’s construct or call signatures (whichever is present).
So again, in the case of an ES6 class, the instance type would be the type of an instance of that class, and in the case of a factory function, it would be the type of the value returned from the function.

```ts
class MyComponent {
render() {}
}

// use a construct signature
constmyComponent = newMyComponent();

// element class type => MyComponent
// element instance type => { render: () => void }

functionMyFactoryFunction() {
return {
render: () => {},
  };
}

// use a call signature
constmyComponent = MyFactoryFunction();

// element class type => MyFactoryFunction
// element instance type => { render: () => void }
```

The element instance type is interesting because it must be assignable to `JSX.ElementClass` or it will result in an error.
By default `JSX.ElementClass` is `{}`, but it can be augmented to limit the use of JSX to only those types that conform to the proper interface.

```ts
declare namespace JSX {
interfaceElementClass {
render: any;
  }
}

classMyComponent {
render() {}
}
functionMyFactoryFunction() {
return { render: () => {} };
}

<MyComponent />; // ok
<MyFactoryFunction />; // ok

classNotAValidComponent {}
functionNotAValidFactoryFunction() {
return {};
}

<NotAValidComponent />; // error
<NotAValidFactoryFunction />; // error
```

### Attribute type checking {#attribute-type-checking}

The first step to type checking attributes is to determine the *element attributes type*.
This is slightly different between intrinsic and value-based elements.

For intrinsic elements, it is the type of the property on `JSX.IntrinsicElements`

```ts
declare namespace JSX {
interfaceIntrinsicElements {
foo: { bar?: boolean };
  }
}

// element attributes type for 'foo' is '{bar?: boolean}'
<foobar />;
```

For value-based elements, it is a bit more complex.
It is determined by the type of a property on the *element instance type* that was previously determined.
Which property to use is determined by `JSX.ElementAttributesProperty`.
It should be declared with a single property.
The name of that property is then used.
As of TypeScript 2.8, if `JSX.ElementAttributesProperty` is not provided, the type of first parameter of the class element’s constructor or Function Component’s call will be used instead.

```ts
declare namespace JSX {
interfaceElementAttributesProperty {
props; // specify the property name to use
  }
}

classMyComponent {
// specify the property on the element instance type
props: {
foo?: string;
  };
}

// element attributes type for 'MyComponent' is '{foo?: string}'
<MyComponentfoo="bar" />;
```

The element attribute type is used to type check the attributes in the JSX.
Optional and required properties are supported.

```ts
declare namespace JSX {
interfaceIntrinsicElements {
foo: { requiredProp: string; optionalProp?: number };
  }
}

<foorequiredProp="bar" />; // ok
<foorequiredProp="bar"optionalProp={0} />; // ok
<foo />; // error, requiredProp is missing
<foorequiredProp={0} />; // error, requiredProp should be a string
<foorequiredProp="bar"unknownProp />; // error, unknownProp does not exist
<foorequiredProp="bar"some-unknown-prop />; // ok, because 'some-unknown-prop' is not a valid identifier
```

> Note: If an attribute name is not a valid JS identifier (like a `data-*` attribute), it is not considered to be an error if it is not found in the element attributes type.
> 

Additionally, the `JSX.IntrinsicAttributes` interface can be used to specify extra properties used by the JSX framework which are not generally used by the components’ props or arguments - for instance `key` in React. Specializing further, the generic `JSX.IntrinsicClassAttributes<T>` type may also be used to specify the same kind of extra attributes just for class components (and not Function Components). In this type, the generic parameter corresponds to the class instance type. In React, this is used to allow the `ref` attribute of type `Ref<T>`. Generally speaking, all of the properties on these interfaces should be optional, unless you intend that users of your JSX framework need to provide some attribute on every tag.

The spread operator also works:

```ts
const props = { requiredProp: "bar" };
<foo {...props} />; // ok

constbadProps = {};
<foo {...badProps} />; // error
```

### Children Type Checking {#children-type-checking}

In TypeScript 2.3, TS introduced type checking of *children*. *children* is a special property in an *element attributes type* where child *JSXExpression*s are taken to be inserted into the attributes.
Similar to how TS uses `JSX.ElementAttributesProperty` to determine the name of *props*, TS uses `JSX.ElementChildrenAttribute` to determine the name of *children* within those props.
`JSX.ElementChildrenAttribute` should be declared with a single property.

```ts
declare namespace JSX {
interfaceElementChildrenAttribute {
children: {}; // specify children name to use
  }
}
```

```ts
<div>
  <h1>Hello</h1>
</div>;

<div>
  <h1>Hello</h1>
World
</div>;

constCustomComp = (props) => <div>{props.children}</div>
<CustomComp>
  <div>HelloWorld</div>
  {"This is just a JS expression..." + 1000}
</CustomComp>
```

You can specify the type of *children* like any other attribute. This will override the default type from, e.g. the [React typings ↗](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react) if you use them.

```ts
interface PropsType {
children: JSX.Element
name: string
}

classComponentextendsReact.Component<PropsType, {}> {
render() {
return (
      <h2>
        {this.props.children}
      </h2>
    )
  }
}

// OK
<Componentname="foo">
  <h1>HelloWorld</h1>
</Component>

// Error: children is of type JSX.Element not array of JSX.Element
<Componentname="bar">
  <h1>HelloWorld</h1>
  <h2>HelloWorld</h2>
</Component>

// Error: children is of type JSX.Element not array of JSX.Element or string.
<Componentname="baz">
  <h1>Hello</h1>
World
</Component>
```

## The JSX result type {#the-jsx-result-type}

By default the result of a JSX expression is typed as `any`.
You can customize the type by specifying the `JSX.Element` interface.
However, it is not possible to retrieve type information about the element, attributes or children of the JSX from this interface.
It is a black box.

## Embedding Expressions {#embedding-expressions}

JSX allows you to embed expressions between tags by surrounding the expressions with curly braces (`{ }`).

```ts
const a = (
  <div>
    {["foo", "bar"].map((i) => (
      <span>{i / 2}</span>
    ))}
  </div>
);
```

The above code will result in an error since you cannot divide a string by a number.
The output, when using the `preserve` option, looks like:

```ts
const a = (
  <div>
    {["foo", "bar"].map(function (i) {
return <span>{i / 2}</span>;
    })}
  </div>
);
```

## React integration {#react-integration}

To use JSX with React you should use the [React typings ↗](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react).
These typings define the `JSX` namespace appropriately for use with React.

```ts
/// <reference path="react.d.ts" />

interfaceProps {
foo: string;
}

classMyComponentextendsReact.Component<Props, {}> {
render() {
return <span>{this.props.foo}</span>;
  }
}

<MyComponentfoo="bar" />; // ok
<MyComponentfoo={0} />; // error
```

### Configuring JSX {#configuring-jsx}

There are multiple compiler flags which can be used to customize your JSX, which work as both a compiler flag and via inline per-file pragmas. To learn more see their tsconfig reference pages:

- [`jsxFactory` ↗](https://www.typescriptlang.org/tsconfig.html#jsxFactory)
- [`jsxFragmentFactory` ↗](https://www.typescriptlang.org/tsconfig.html#jsxFragmentFactory)
- [`jsxImportSource` ↗](https://www.typescriptlang.org/tsconfig.html#jsxImportSource)
