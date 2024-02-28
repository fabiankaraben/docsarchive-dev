---
linkTitle: "CSS-in-JS"
title: "Styling: CSS-in-JS | Next.js"
description: "Use CSS-in-JS libraries with Next.js"
weight: 3
type: docs
---

# CSS-in-JS

It's possible to use any existing CSS-in-JS solution.The simplest one is inline styles:

```
function HiThere() {
  return <p style={{ color: 'red' }}>hi there</p>
}
 
export default HiThere
```

We bundle [styled-jsx ↗](https://github.com/vercel/styled-jsx) to provide support for isolated scoped CSS.
The aim is to support "shadow CSS" similar to Web Components, which unfortunately [do not support server-rendering and are JS-only ↗](https://github.com/w3c/webcomponents/issues/71).

See the above examples for other popular CSS-in-JS solutions (like Styled Components).

A component using `styled-jsx` looks like this:

```
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}
 
export default HelloWorld
```

Please see the [styled-jsx documentation ↗](https://github.com/vercel/styled-jsx) for more examples.

### Disabling JavaScript {#disabling-javascript}

Yes, if you disable JavaScript the CSS will still be loaded in the production build (`next start`). During development, we require JavaScript to be enabled to provide the best developer experience with [Fast Refresh ↗](https://nextjs.org/blog/next-9-4#fast-refresh).
