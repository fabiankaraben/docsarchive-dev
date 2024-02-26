---
linkTitle: "React"
title: "React Top-Level API – React"
description: ""
weight: 1
type: docs
prev: /react/18.1/advanced-guides/web-components
---

# React Top-Level API

`React` is the entry point to the React library. If you load React from a `<script>` tag, these top-level APIs are available on the `React` global. If you use ES6 with npm, you can write `import React from 'react'`. If you use ES5 with npm, you can write `var React = require('react')`.

## Overview {#overview}

### Components {#components}

React components let you split the UI into independent, reusable pieces, and think about each piece in isolation. React components can be defined by subclassing `React.Component` or `React.PureComponent`.

- [`React.Component`](/react/18.1/api-reference/react-api#reactcomponent)
- [`React.PureComponent`](/react/18.1/api-reference/react-api#reactpurecomponent)

If you don’t use ES6 classes, you may use the `create-react-class` module instead. See [Using React without ES6](/react/18.1/advanced-guides/react-without-es6) for more information.

React components can also be defined as functions which can be wrapped:

- [`React.memo`](/react/18.1/api-reference/react-api#reactmemo)

### Creating React Elements {#creating-react-elements}

We recommend [using JSX](/react/18.1/main-concepts/introducing-jsx) to describe what your UI should look like. Each JSX element is just syntactic sugar for calling [`React.createElement()`](/react/18.1/api-reference/react-api#createelement). You will not typically invoke the following methods directly if you are using JSX.

- [`createElement()`](/react/18.1/api-reference/react-api#createelement)
- [`createFactory()`](/react/18.1/api-reference/react-api#createfactory)

See [Using React without JSX](/react/18.1/advanced-guides/react-without-jsx) for more information.

### Transforming Elements {#transforming-elements}

`React` provides several APIs for manipulating elements:

- [`cloneElement()`](/react/18.1/api-reference/react-api#cloneelement)
- [`isValidElement()`](/react/18.1/api-reference/react-api#isvalidelement)
- [`React.Children`](/react/18.1/api-reference/react-api#reactchildren)

### Fragments {#fragments}

`React` also provides a component for rendering multiple elements without a wrapper.

- [`React.Fragment`](/react/18.1/api-reference/react-api#reactfragment)

### Refs {#refs}

- [`React.createRef`](/react/18.1/api-reference/react-api#reactcreateref)
- [`React.forwardRef`](/react/18.1/api-reference/react-api#reactforwardref)

### Suspense {#suspense}

Suspense lets components “wait” for something before rendering. Today, Suspense only supports one use case: [loading components dynamically with `React.lazy`](/react/18.1/advanced-guides/code-splitting#reactlazy). In the future, it will support other use cases like data fetching.

- [`React.lazy`](/react/18.1/api-reference/react-api#reactlazy)
- [`React.Suspense`](/react/18.1/api-reference/react-api#reactsuspense)

### Transitions {#transitions}

*Transitions* are a new concurrent feature introduced in React 18. They allow you to mark updates as transitions, which tells React that they can be interrupted and avoid going back to Suspense fallbacks for already visible content.

- [`React.startTransition`](/react/18.1/api-reference/react-api#starttransition)
- [`React.useTransition`](/react/18.1/hooks/hooks-reference#usetransition)

### Hooks {#hooks}

*Hooks* are a new addition in React 16.8. They let you use state and other React features without writing a class. Hooks have a [dedicated docs section](/react/18.1/hooks/hooks-intro) and a separate API reference:

- [Basic Hooks](/react/18.1/hooks/hooks-reference#basic-hooks)
  - [`useState`](/react/18.1/hooks/hooks-reference#usestate)
  - [`useEffect`](/react/18.1/hooks/hooks-reference#useeffect)
  - [`useContext`](/react/18.1/hooks/hooks-reference#usecontext)
- [Additional Hooks](/react/18.1/hooks/hooks-reference#additional-hooks)
  - [`useReducer`](/react/18.1/hooks/hooks-reference#usereducer)
  - [`useCallback`](/react/18.1/hooks/hooks-reference#usecallback)
  - [`useMemo`](/react/18.1/hooks/hooks-reference#usememo)
  - [`useRef`](/react/18.1/hooks/hooks-reference#useref)
  - [`useImperativeHandle`](/react/18.1/hooks/hooks-reference#useimperativehandle)
  - [`useLayoutEffect`](/react/18.1/hooks/hooks-reference#uselayouteffect)
  - [`useDebugValue`](/react/18.1/hooks/hooks-reference#usedebugvalue)
  - [`useDeferredValue`](/react/18.1/hooks/hooks-reference#usedeferredvalue)
  - [`useTransition`](/react/18.1/hooks/hooks-reference#usetransition)
  - [`useId`](/react/18.1/hooks/hooks-reference#useid)
- [Library Hooks](/react/18.1/hooks/hooks-reference#library-hooks)
  - [`useSyncExternalStore`](/react/18.1/hooks/hooks-reference#usesyncexternalstore)
  - [`useInsertionEffect`](/react/18.1/hooks/hooks-reference#useinsertioneffect)

---

## Reference {#reference}

### `React.Component` {#reactcomponent}

`React.Component` is the base class for React components when they are defined using [ES6 classes ↗](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes):

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

See the [React.Component API Reference ↗](https://reactjs.org/docs/react-component.html) for a list of methods and properties related to the base `React.Component` class.

---

### `React.PureComponent` {#reactpurecomponent}

`React.PureComponent` is similar to [`React.Component`](/react/18.1/api-reference/react-api#reactcomponent). The difference between them is that [`React.Component`](/react/18.1/api-reference/react-api#reactcomponent) doesn’t implement [`shouldComponentUpdate()` ↗](https://reactjs.org/docs/react-component.html#shouldcomponentupdate), but `React.PureComponent` implements it with a shallow prop and state comparison.

If your React component’s `render()` function renders the same result given the same props and state, you can use `React.PureComponent` for a performance boost in some cases.

> Note
> `React.PureComponent`’s `shouldComponentUpdate()` only shallowly compares the objects. If these contain complex data structures, it may produce false-negatives for deeper differences. Only extend `PureComponent` when you expect to have simple props and state, or use [`forceUpdate()` ↗](https://reactjs.org/docs/react-component.html#forceupdate) when you know deep data structures have changed. Or, consider using [immutable objects ↗](https://facebook.github.io/immutable-js/) to facilitate fast comparisons of nested data.
> Furthermore, `React.PureComponent`’s `shouldComponentUpdate()` skips prop updates for the whole component subtree. Make sure all the children components are also “pure”.
> 

---

### `React.memo` {#reactmemo}

```jsx
const MyComponent = React.memo(function MyComponent(props) {
  /* render using props */
});
```

`React.memo` is a [higher order component](/react/18.1/advanced-guides/higher-order-components).

If your component renders the same result given the same props, you can wrap it in a call to `React.memo` for a performance boost in some cases by memoizing the result. This means that React will skip rendering the component, and reuse the last rendered result.

`React.memo` only checks for prop changes. If your function component wrapped in `React.memo` has a [`useState`](/react/18.1/hooks/hooks-state), [`useReducer`](/react/18.1/hooks/hooks-reference#usereducer) or [`useContext`](/react/18.1/hooks/hooks-reference#usecontext) Hook in its implementation, it will still rerender when state or context change.

By default it will only shallowly compare complex objects in the props object. If you want control over the comparison, you can also provide a custom comparison function as the second argument.

```jsx
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual);
```

This method only exists as a **[performance optimization](/react/18.1/advanced-guides/optimizing-performance).** Do not rely on it to “prevent” a render, as this can lead to bugs.

> Note
> Unlike the [`shouldComponentUpdate()` ↗](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) method on class components, the `areEqual` function returns `true` if the props are equal and `false` if the props are not equal. This is the inverse from `shouldComponentUpdate`.
> 

---

### `createElement()` {#createelement}

```jsx
React.createElement(
  type,
  [props],
  [...children]
)
```

Create and return a new [React element](/react/18.1/main-concepts/rendering-elements) of the given type. The type argument can be either a tag name string (such as `'div'` or `'span'`), a [React component](/react/18.1/main-concepts/components-and-props) type (a class or a function), or a [React fragment](/react/18.1/api-reference/react-api#reactfragment) type.

Code written with [JSX](/react/18.1/main-concepts/introducing-jsx) will be converted to use `React.createElement()`. You will not typically invoke `React.createElement()` directly if you are using JSX. See [React Without JSX](/react/18.1/advanced-guides/react-without-jsx) to learn more.

---

### `cloneElement()` {#cloneelement}

```text
React.cloneElement(
  element,
  [config],
  [...children]
)
```

Clone and return a new React element using `element` as the starting point. `config` should contain all new props, `key`, or `ref`. The resulting element will have the original element’s props with the new props merged in shallowly. New children will replace existing children. `key` and `ref` from the original element will be preserved if no `key` and `ref` present in the `config`.

`React.cloneElement()` is almost equivalent to:

```jsx
<element.type {...element.props} {...props}>{children}</element.type>
```

However, it also preserves `ref`s. This means that if you get a child with a `ref` on it, you won’t accidentally steal it from your ancestor. You will get the same `ref` attached to your new element. The new `ref` or `key` will replace old ones if present.

This API was introduced as a replacement of the deprecated `React.addons.cloneWithProps()`.

---

### `createFactory()` {#createfactory}

```jsx
React.createFactory(type)
```

Return a function that produces React elements of a given type. Like [`React.createElement()`](/react/18.1/api-reference/react-api#createelement), the type argument can be either a tag name string (such as `'div'` or `'span'`), a [React component](/react/18.1/main-concepts/components-and-props) type (a class or a function), or a [React fragment](/react/18.1/api-reference/react-api#reactfragment) type.

This helper is considered legacy, and we encourage you to either use JSX or use `React.createElement()` directly instead.

You will not typically invoke `React.createFactory()` directly if you are using JSX. See [React Without JSX](/react/18.1/advanced-guides/react-without-jsx) to learn more.

---

### `isValidElement()` {#isvalidelement}

```jsx
React.isValidElement(object)
```

Verifies the object is a React element. Returns `true` or `false`.

---

### `React.Children` {#reactchildren}

`React.Children` provides utilities for dealing with the `this.props.children` opaque data structure.

#### `React.Children.map` {#reactchildrenmap}

```jsx
React.Children.map(children, function[(thisArg)])
```

Invokes a function on every immediate child contained within `children` with `this` set to `thisArg`. If `children` is an array it will be traversed and the function will be called for each child in the array. If children is `null` or `undefined`, this method will return `null` or `undefined` rather than an array.

> Note
> If `children` is a `Fragment` it will be treated as a single child and not traversed.
> 

#### `React.Children.forEach` {#reactchildrenforeach}

```jsx
React.Children.forEach(children, function[(thisArg)])
```

Like [`React.Children.map()`](/react/18.1/api-reference/react-api#reactchildrenmap) but does not return an array.

#### `React.Children.count` {#reactchildrencount}

```jsx
React.Children.count(children)
```

Returns the total number of components in `children`, equal to the number of times that a callback passed to `map` or `forEach` would be invoked.

#### `React.Children.only` {#reactchildrenonly}

```jsx
React.Children.only(children)
```

Verifies that `children` has only one child (a React element) and returns it. Otherwise this method throws an error.

> Note:
> `React.Children.only()` does not accept the return value of [`React.Children.map()`](/react/18.1/api-reference/react-api#reactchildrenmap) because it is an array rather than a React element.
> 

#### `React.Children.toArray` {#reactchildrentoarray}

```jsx
React.Children.toArray(children)
```

Returns the `children` opaque data structure as a flat array with keys assigned to each child. Useful if you want to manipulate collections of children in your render methods, especially if you want to reorder or slice `this.props.children` before passing it down.

> Note:
> `React.Children.toArray()` changes keys to preserve the semantics of nested arrays when flattening lists of children. That is, `toArray` prefixes each key in the returned array so that each element’s key is scoped to the input array containing it.
> 

---

### `React.Fragment` {#reactfragment}

The `React.Fragment` component lets you return multiple elements in a `render()` method without creating an additional DOM element:

```jsx
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```

You can also use it with the shorthand `<></>` syntax. For more information, see [React v16.2.0: Improved Support for Fragments ↗](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html).

### `React.createRef` {#reactcreateref}

`React.createRef` creates a [ref](/react/18.1/advanced-guides/refs-and-the-dom) that can be attached to React elements via the ref attribute.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.inputRef = React.createRef();  }

  render() {
    return <input type="text" ref={this.inputRef} />;  }

  componentDidMount() {
    this.inputRef.current.focus();  }
}
```



### `React.forwardRef` {#reactforwardref}

`React.forwardRef` creates a React component that forwards the [ref](/react/18.1/advanced-guides/refs-and-the-dom) attribute it receives to another component below in the tree. This technique is not very common but is particularly useful in two scenarios:

- [Forwarding refs to DOM components](/react/18.1/advanced-guides/forwarding-refs#forwarding-refs-to-dom-components)
- [Forwarding refs in higher-order-components](/react/18.1/advanced-guides/forwarding-refs#forwarding-refs-in-higher-order-components)

`React.forwardRef` accepts a rendering function as an argument. React will call this function with `props` and `ref` as two arguments. This function should return a React node.



```jsx
const FancyButton = React.forwardRef((props, ref) => (  <button ref={ref} className="FancyButton">    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```



In the above example, React passes a `ref` given to `<FancyButton ref={ref}>` element as a second argument to the rendering function inside the `React.forwardRef` call. This rendering function passes the `ref` to the `<button ref={ref}>` element.

As a result, after React attaches the ref, `ref.current` will point directly to the `<button>` DOM element instance.

For more information, see [forwarding refs](/react/18.1/advanced-guides/forwarding-refs).

### `React.lazy` {#reactlazy}

`React.lazy()` lets you define a component that is loaded dynamically. This helps reduce the bundle size to delay loading components that aren’t used during the initial render.

You can learn how to use it from our [code splitting documentation](/react/18.1/advanced-guides/code-splitting#reactlazy). You might also want to check out [this article ↗](https://medium.com/@pomber/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d) explaining how to use it in more detail.

```jsx
// This component is loaded dynamically
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

Note that rendering `lazy` components requires that there’s a `<React.Suspense>` component higher in the rendering tree. This is how you specify a loading indicator.

### `React.Suspense` {#reactsuspense}

`React.Suspense` lets you specify the loading indicator in case some components in the tree below it are not yet ready to render. In the future we plan to let `Suspense` handle more scenarios such as data fetching. You can read about this in [our roadmap ↗](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html).

Today, lazy loading components is the **only** use case supported by `<React.Suspense>`:

```jsx
// This component is loaded dynamically
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Displays <Spinner> until OtherComponent loads
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

It is documented in our [code splitting guide](/react/18.1/advanced-guides/code-splitting#reactlazy). Note that `lazy` components can be deep inside the `Suspense` tree — it doesn’t have to wrap every one of them. The best practice is to place `<Suspense>` where you want to see a loading indicator, but to use `lazy()` wherever you want to do code splitting.

> Note
> For content that is already shown to the user, switching back to a loading indicator can be disorienting. It is sometimes better to show the “old” UI while the new UI is being prepared. To do this, you can use the new transition APIs [`startTransition`](/react/18.1/api-reference/react-api#starttransition) and [`useTransition`](/react/18.1/hooks/hooks-reference#usetransition) to mark updates as transitions and avoid unexpected fallbacks.
> 

#### `React.Suspense` in Server Side Rendering {#reactsuspense-in-server-side-rendering}

During server side rendering Suspense Boundaries allow you to flush your application in smaller chunks by suspending.
When a component suspends we schedule a low priority task to render the closest Suspense boundary’s fallback. If the component unsuspends before we flush the fallback then we send down the actual content and throw away the fallback.

#### `React.Suspense` during hydration {#reactsuspense-during-hydration}

Suspense boundaries depend on their parent boundaries being hydrated before they can hydrate, but they can hydrate independently from sibling boundaries. Events on a boundary before its hydrated will cause the boundary to hydrate at
a higher priority than neighboring boundaries. [Read more ↗](https://github.com/reactwg/react-18/discussions/130)

### `React.startTransition` {#starttransition}

```jsx
React.startTransition(callback)
```

`React.startTransition` lets you mark updates inside the provided callback as transitions. This method is designed to be used when [`React.useTransition`](/react/18.1/hooks/hooks-reference#usetransition) is not available.

> Note:
> Updates in a transition yield to more urgent updates such as clicks.
> Updates in a transition will not show a fallback for re-suspended content, allowing the user to continue interacting while rendering the update.
> `React.startTransition` does not provide an `isPending` flag. To track the pending status of a transition see [`React.useTransition`](/react/18.1/hooks/hooks-reference#usetransition).
> 
