---
linkTitle: "React Without JSX"
title: "React Without JSX – React"
description: ""
weight: 14
type: docs
---

# React Without JSX

JSX is not a requirement for using React. Using React without JSX is especially convenient when you don’t want to set up compilation in your build environment.

Each JSX element is just syntactic sugar for calling `React.createElement(component, props, ...children)`. So, anything you can do with JSX can also be done with just plain JavaScript.

For example, this code written with JSX:

```jsx
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Hello toWhat="World" />);
```

can be compiled to this code that does not use JSX:

```jsx
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(React.createElement(Hello, {toWhat: 'World'}, null));
```

If you’re curious to see more examples of how JSX is converted to JavaScript, you can try out [the online Babel compiler ↗](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA).

The component can either be provided as a string, as a subclass of `React.Component`, or a plain function.

If you get tired of typing `React.createElement` so much, one common pattern is to assign a shorthand:

```jsx
const e = React.createElement;

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(e('div', null, 'Hello World'));
```

If you use this shorthand form for `React.createElement`, it can be almost as convenient to use React without JSX.

Alternatively, you can refer to community projects such as [`react-hyperscript` ↗](https://github.com/mlmorg/react-hyperscript) and [`hyperscript-helpers` ↗](https://github.com/ohanhi/hyperscript-helpers) which offer a terser syntax.
