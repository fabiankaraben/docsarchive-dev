---
linkTitle: "Custom Document"
title: "Routing: Custom Document | Next.js"
description: "Extend the default document markup added by Next.js."
weight: 5
type: docs
---

# Custom Document

A custom `Document` can update the `<html>` and `<body>` tags used to render a [Page](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts).

To override the default `Document`, create the file `pages/_document` as shown below:


pages/_document.tsx
```
import { Html, Head, Main, NextScript } from 'next/document'
 
export default function Document() {
  return (
    <Html lang="en">
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

> **Good to know**
> - `_document` is only rendered on the server, so event handlers like `onClick` cannot be used in this file.
> - `<Html>`, `<Head />`, `<Main />` and `<NextScript />` are required for the page to be properly rendered.
> 

## Caveats {#caveats}

- The `<Head />` component used in `_document` is not the same as [`next/head`](/nextjs/13.5/using-pages-router/api-reference/components/head). The `<Head />` component used here should only be used for any `<head>` code that is common for all pages. For all other cases, such as `<title>` tags, we recommend using [`next/head`](/nextjs/13.5/using-pages-router/api-reference/components/head) in your pages or components.
- React components outside of `<Main />` will not be initialized by the browser. Do *not* add application logic here or custom CSS (like `styled-jsx`). If you need shared components in all your pages (like a menu or a toolbar), read [Layouts](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts#layout-pattern) instead.
- `Document` currently does not support Next.js [Data Fetching methods ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching.html) like [`getStaticProps`](/nextjs/13.5/using-pages-router/building-your-application/data-fetching/get-static-props) or [`getServerSideProps` ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching/get-server-side-props.html).

## Customizing `renderPage` {#customizing-renderpage}

Customizing `renderPage` is advanced and only needed for libraries like CSS-in-JS to support server-side rendering. This is not needed for built-in `styled-jsx` support.

**We do not recommend using this pattern.** Instead, consider [incrementally adopting](/nextjs/13.5/using-app-router/building-your-application/upgrading/app-router-migration) the App Router, which allows you to more easily fetch data for [pages and layouts](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts).


pages/_document.tsx
```
import Document, {
  Html,
  Head,
  Main,
  NextScript,
  DocumentContext,
  DocumentInitialProps,
} from 'next/document'
 
class MyDocument extends Document {
  static async getInitialProps(
    ctx: DocumentContext
  ): Promise<DocumentInitialProps> {
    const originalRenderPage = ctx.renderPage
 
    // Run the React rendering logic synchronously
    ctx.renderPage = () =>
      originalRenderPage({
        // Useful for wrapping the whole react tree
        enhanceApp: (App) => App,
        // Useful for wrapping in a per-page basis
        enhanceComponent: (Component) => Component,
      })
 
    // Run the parent `getInitialProps`, it now includes the custom `renderPage`
    const initialProps = await Document.getInitialProps(ctx)
 
    return initialProps
  }
 
  render() {
    return (
      <Html lang="en">
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}
 
export default MyDocument
```

> **Good to know**
> - `getInitialProps` in `_document` is not called during client-side transitions.
> - The `ctx` object for `_document` is equivalent to the one received in [`getInitialProps`](/nextjs/13.5/using-pages-router/api-reference/functions/get-initial-props#context-object), with the addition of `renderPage`.
> 
