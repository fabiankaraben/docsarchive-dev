---
linkTitle: "Custom App"
title: "Routing: Custom App | Next.js"
description: "Control page initialization and add a layout that persists for all pages by overriding the default App component used by Next.js."
weight: 4
type: docs
---

# Custom App

Next.js uses the `App` component to initialize pages. You can override it and control the page initialization and:

- Create a shared layout between page changes
- Inject additional data into pages
- [Add global CSS ↗](https://nextjs.org/docs/pages/building-your-application/styling.html)

## Usage {#usage}

To override the default `App`, create the file `pages/_app` as shown below:


pages/_app.tsx
```
import type { AppProps } from 'next/app'
 
export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

The `Component` prop is the active `page`, so whenever you navigate between routes, `Component` will change to the new `page`. Therefore, any props you send to `Component` will be received by the `page`.

`pageProps` is an object with the initial props that were preloaded for your page by one of our [data fetching methods ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching.html), otherwise it's an empty object.

> **Good to know**
> - If your app is running and you added a custom `App`, you'll need to restart the development server. Only required if `pages/_app.js` didn't exist before.
> - `App` does not support Next.js [Data Fetching methods ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching.html) like [`getStaticProps`](/nextjs/13.5/using-pages-router/building-your-application/data-fetching/get-static-props) or [`getServerSideProps` ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching/get-server-side-props.html).
> 

## `getInitialProps` with `App` {#getinitialprops-with-app}

Using [`getInitialProps`](/nextjs/13.5/using-pages-router/api-reference/functions/get-initial-props) in `App` will disable [Automatic Static Optimization](/nextjs/13.5/using-pages-router/building-your-application/rendering/automatic-static-optimization) for pages without [`getStaticProps`](/nextjs/13.5/using-pages-router/building-your-application/data-fetching/get-static-props).

**We do not recommend using this pattern.** Instead, consider [incrementally adopting](/nextjs/13.5/using-app-router/building-your-application/upgrading/app-router-migration) the App Router, which allows you to more easily fetch data for [pages and layouts](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts).


pages/_app.tsx
```
import App, { AppContext, AppInitialProps, AppProps } from 'next/app'
 
type AppOwnProps = { example: string }
 
export default function MyApp({
  Component,
  pageProps,
  example,
}: AppProps & AppOwnProps) {
  return (
    <>
      <p>Data: {example}</p>
      <Component {...pageProps} />
    </>
  )
}
 
MyApp.getInitialProps = async (
  context: AppContext
): Promise<AppOwnProps & AppInitialProps> => {
  const ctx = await App.getInitialProps(context)
 
  return { ...ctx, example: 'data' }
}
```
