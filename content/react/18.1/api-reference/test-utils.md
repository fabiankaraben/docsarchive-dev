---
linkTitle: "Test Utilities"
title: "Test Utilities – React"
description: ""
weight: 7
type: docs
---

# Test Utilities

**Importing**

```jsx
import ReactTestUtils from 'react-dom/test-utils'; // ES6
var ReactTestUtils = require('react-dom/test-utils'); // ES5 with npm
```

## Overview {#overview}

`ReactTestUtils` makes it easy to test React components in the testing framework of your choice. At Facebook we use [Jest ↗](https://facebook.github.io/jest/) for painless JavaScript testing. Learn how to get started with Jest through the Jest website’s [React Tutorial ↗](https://jestjs.io/docs/tutorial-react).

> Note:
> We recommend using [React Testing Library ↗](https://testing-library.com/react) which is designed to enable and encourage writing tests that use your components as the end users do.
> For React versions <= 16, the [Enzyme ↗](https://airbnb.io/enzyme/) library makes it easy to assert, manipulate, and traverse your React Components’ output.
> 

- [`act()`](/react/18.1/api-reference/test-utils#act)
- [`mockComponent()`](/react/18.1/api-reference/test-utils#mockcomponent)
- [`isElement()`](/react/18.1/api-reference/test-utils#iselement)
- [`isElementOfType()`](/react/18.1/api-reference/test-utils#iselementoftype)
- [`isDOMComponent()`](/react/18.1/api-reference/test-utils#isdomcomponent)
- [`isCompositeComponent()`](/react/18.1/api-reference/test-utils#iscompositecomponent)
- [`isCompositeComponentWithType()`](/react/18.1/api-reference/test-utils#iscompositecomponentwithtype)
- [`findAllInRenderedTree()`](/react/18.1/api-reference/test-utils#findallinrenderedtree)
- [`scryRenderedDOMComponentsWithClass()`](/react/18.1/api-reference/test-utils#scryrendereddomcomponentswithclass)
- [`findRenderedDOMComponentWithClass()`](/react/18.1/api-reference/test-utils#findrendereddomcomponentwithclass)
- [`scryRenderedDOMComponentsWithTag()`](/react/18.1/api-reference/test-utils#scryrendereddomcomponentswithtag)
- [`findRenderedDOMComponentWithTag()`](/react/18.1/api-reference/test-utils#findrendereddomcomponentwithtag)
- [`scryRenderedComponentsWithType()`](/react/18.1/api-reference/test-utils#scryrenderedcomponentswithtype)
- [`findRenderedComponentWithType()`](/react/18.1/api-reference/test-utils#findrenderedcomponentwithtype)
- [`renderIntoDocument()`](/react/18.1/api-reference/test-utils#renderintodocument)
- [`Simulate`](/react/18.1/api-reference/test-utils#simulate)

## Reference {#reference}

### `act()` {#act}

To prepare a component for assertions, wrap the code rendering it and performing updates inside an `act()` call. This makes your test run closer to how React works in the browser.

> Note
> If you use `react-test-renderer`, it also provides an `act` export that behaves the same way.
> 

For example, let’s say we have this `Counter` component:

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 0};
    this.handleClick = this.handleClick.bind(this);
  }
  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }
  handleClick() {
    this.setState(state => ({
      count: state.count + 1,
    }));
  }
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={this.handleClick}>
          Click me
        </button>
      </div>
    );
  }
}
```

Here is how we can test it:

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { act } from 'react-dom/test-utils';import Counter from './Counter';

let container;

beforeEach(() => {
  container = document.createElement('div');
  document.body.appendChild(container);
});

afterEach(() => {
  document.body.removeChild(container);
  container = null;
});

it('can render and update a counter', () => {
  // Test first render and componentDidMount
  act(() => {    ReactDOM.createRoot(container).render(<Counter />);  });  const button = container.querySelector('button');
  const label = container.querySelector('p');
  expect(label.textContent).toBe('You clicked 0 times');
  expect(document.title).toBe('You clicked 0 times');

  // Test second render and componentDidUpdate
  act(() => {    button.dispatchEvent(new MouseEvent('click', {bubbles: true}));  });  expect(label.textContent).toBe('You clicked 1 times');
  expect(document.title).toBe('You clicked 1 times');
});
```

- Don’t forget that dispatching DOM events only works when the DOM container is added to the `document`. You can use a library like [React Testing Library ↗](https://testing-library.com/react) to reduce the boilerplate code.
- The [`recipes`](/react/18.1/testing/testing-recipes) document contains more details on how `act()` behaves, with examples and usage.

---

### `mockComponent()` {#mockcomponent}

```jsx
mockComponent(
  componentClass,
  [mockTagName]
)
```

Pass a mocked component module to this method to augment it with useful methods that allow it to be used as a dummy React component. Instead of rendering as usual, the component will become a simple `<div>` (or other tag if `mockTagName` is provided) containing any provided children.

> Note:
> `mockComponent()` is a legacy API. We recommend using [`jest.mock()` ↗](https://jestjs.io/docs/tutorial-react-native#mock-native-modules-using-jestmock) instead.
> 

---

### `isElement()` {#iselement}

```jsx
isElement(element)
```

Returns `true` if `element` is any React element.

---

### `isElementOfType()` {#iselementoftype}

```jsx
isElementOfType(
  element,
  componentClass
)
```

Returns `true` if `element` is a React element whose type is of a React `componentClass`.

---

### `isDOMComponent()` {#isdomcomponent}

```jsx
isDOMComponent(instance)
```

Returns `true` if `instance` is a DOM component (such as a `<div>` or `<span>`).

---

### `isCompositeComponent()` {#iscompositecomponent}

```jsx
isCompositeComponent(instance)
```

Returns `true` if `instance` is a user-defined component, such as a class or a function.

---

### `isCompositeComponentWithType()` {#iscompositecomponentwithtype}

```jsx
isCompositeComponentWithType(
  instance,
  componentClass
)
```

Returns `true` if `instance` is a component whose type is of a React `componentClass`.

---

### `findAllInRenderedTree()` {#findallinrenderedtree}

```jsx
findAllInRenderedTree(
  tree,
  test
)
```

Traverse all components in `tree` and accumulate all components where `test(component)` is `true`. This is not that useful on its own, but it’s used as a primitive for other test utils.

---

### `scryRenderedDOMComponentsWithClass()` {#scryrendereddomcomponentswithclass}

```jsx
scryRenderedDOMComponentsWithClass(
  tree,
  className
)
```

Finds all DOM elements of components in the rendered tree that are DOM components with the class name matching `className`.

---

### `findRenderedDOMComponentWithClass()` {#findrendereddomcomponentwithclass}

```jsx
findRenderedDOMComponentWithClass(
  tree,
  className
)
```

Like [`scryRenderedDOMComponentsWithClass()`](/react/18.1/api-reference/test-utils#scryrendereddomcomponentswithclass) but expects there to be one result, and returns that one result, or throws exception if there is any other number of matches besides one.

---

### `scryRenderedDOMComponentsWithTag()` {#scryrendereddomcomponentswithtag}

```jsx
scryRenderedDOMComponentsWithTag(
  tree,
  tagName
)
```

Finds all DOM elements of components in the rendered tree that are DOM components with the tag name matching `tagName`.

---

### `findRenderedDOMComponentWithTag()` {#findrendereddomcomponentwithtag}

```jsx
findRenderedDOMComponentWithTag(
  tree,
  tagName
)
```

Like [`scryRenderedDOMComponentsWithTag()`](/react/18.1/api-reference/test-utils#scryrendereddomcomponentswithtag) but expects there to be one result, and returns that one result, or throws exception if there is any other number of matches besides one.

---

### `scryRenderedComponentsWithType()` {#scryrenderedcomponentswithtype}

```jsx
scryRenderedComponentsWithType(
  tree,
  componentClass
)
```

Finds all instances of components with type equal to `componentClass`.

---

### `findRenderedComponentWithType()` {#findrenderedcomponentwithtype}

```jsx
findRenderedComponentWithType(
  tree,
  componentClass
)
```

Same as [`scryRenderedComponentsWithType()`](/react/18.1/api-reference/test-utils#scryrenderedcomponentswithtype) but expects there to be one result and returns that one result, or throws exception if there is any other number of matches besides one.

---

### `renderIntoDocument()` {#renderintodocument}

```jsx
renderIntoDocument(element)
```

Render a React element into a detached DOM node in the document. **This function requires a DOM.** It is effectively equivalent to:

```jsx
const domContainer = document.createElement('div');
ReactDOM.createRoot(domContainer).render(element);
```

> Note:
> You will need to have `window`, `window.document` and `window.document.createElement` globally available **before** you import `React`. Otherwise React will think it can’t access the DOM and methods like `setState` won’t work.
> 

---

## Other Utilities {#other-utilities}

### `Simulate` {#simulate}

```jsx
Simulate.{eventName}(
  element,
  [eventData]
)
```

Simulate an event dispatch on a DOM node with optional `eventData` event data.

`Simulate` has a method for [every event that React understands](/react/18.1/api-reference/events#supported-events).

**Clicking an element**

```jsx
// <button ref={(node) => this.button = node}>...</button>
const node = this.button;
ReactTestUtils.Simulate.click(node);
```

**Changing the value of an input field and then pressing ENTER.**

```jsx
// <input ref={(node) => this.textInput = node} />
const node = this.textInput;
node.value = 'giraffe';
ReactTestUtils.Simulate.change(node);
ReactTestUtils.Simulate.keyDown(node, {key: "Enter", keyCode: 13, which: 13});
```

> Note
> You will have to provide any event property that you’re using in your component (e.g. keyCode, which, etc…) as React is not creating any of these for you.
> 

---
