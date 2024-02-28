---
linkTitle: "Lazy Loading"
title: "Optimizing: Lazy Loading | Next.js"
description: "Lazy load imported libraries and React Components to improve your application's loading performance."
weight: 5
type: docs
---

# Lazy Loading

[Lazy loading ↗](https://developer.mozilla.org/docs/Web/Performance/Lazy_loading) in Next.js helps improve the initial loading performance of an application by decreasing the amount of JavaScript needed to render a route.

It allows you to defer loading of **Client Components** and imported libraries, and only include them in the client bundle when they're needed. For example, you might want to defer loading a modal until a user clicks to open it.

There are two ways you can implement lazy loading in Next.js:

1. Using [Dynamic Imports](/nextjs/13.5/using-pages-router/building-your-application/optimizing/lazy-loading#nextdynamic) with `next/dynamic`
2. Using [`React.lazy()` ↗](https://react.dev/reference/react/lazy) with [Suspense ↗](https://react.dev/reference/react/Suspense)

By default, Server Components are automatically [code split ↗](https://developer.mozilla.org/docs/Glossary/Code_splitting), and you can use [streaming](/nextjs/13.5/using-app-router/building-your-application/routing/loading-ui-and-streaming) to progressively send pieces of UI from the server to the client. Lazy loading applies to Client Components.

## `next/dynamic` {#nextdynamic}

`next/dynamic` is a composite of [`React.lazy()` ↗](https://react.dev/reference/react/lazy) and [Suspense ↗](https://react.dev/reference/react/Suspense). It behaves the same way in the `app` and `pages` directories to allow for incremental migration.

## Examples {#examples}

By using `next/dynamic`, the header component will not be included in the page's initial JavaScript bundle. The page will render the Suspense `fallback` first, followed by the `Header` component when the `Suspense` boundary is resolved.

```
import dynamic from 'next/dynamic'
 
const DynamicHeader = dynamic(() => import('../components/header'), {
  loading: () => <p>Loading...</p>,
})
 
export default function Home() {
  return <DynamicHeader />
}
```

> **Good to know**: In `import('path/to/component')`, the path must be explicitly written. It can't be a template string nor a variable. Furthermore the `import()` has to be inside the `dynamic()` call for Next.js to be able to match webpack bundles / module ids to the specific `dynamic()` call and preload them before rendering. `dynamic()` can't be used inside of React rendering as it needs to be marked in the top level of the module for preloading to work, similar to `React.lazy`.
> 

## With named exports {#with-named-exports}

To dynamically import a named export, you can return it from the [Promise ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) returned by [`import()` ↗](https://github.com/tc39/proposal-dynamic-import#example):


components/hello.js
```
export function Hello() {
  return <p>Hello!</p>
}
 
// pages/index.js
import dynamic from 'next/dynamic'
 
const DynamicComponent = dynamic(() =>
  import('../components/hello').then((mod) => mod.Hello)
)
```

## With no SSR {#with-no-ssr}

To dynamically load a component on the client side, you can use the `ssr` option to disable server-rendering. This is useful if an external dependency or component relies on browser APIs like `window`.

```
import dynamic from 'next/dynamic'
 
const DynamicHeader = dynamic(() => import('../components/header'), {
  ssr: false,
})
```

## With external libraries {#with-external-libraries}

This example uses the external library `fuse.js` for fuzzy search. The module is only loaded in the browser after the user types in the search input.

```
import { useState } from 'react'
 
const names = ['Tim', 'Joe', 'Bel', 'Lee']
 
export default function Page() {
  const [results, setResults] = useState()
 
  return (
    <div>
      <input
        type="text"
        placeholder="Search"
        onChange={async (e) => {
          const { value } = e.currentTarget
          // Dynamically load fuse.js
          const Fuse = (await import('fuse.js')).default
          const fuse = new Fuse(names)
 
          setResults(fuse.search(value))
        }}
      />
      <pre>Results: {JSON.stringify(results, null, 2)}</pre>
    </div>
  )
}
```
