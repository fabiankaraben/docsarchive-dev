---
linkTitle: "TypeScript"
title: "Configuring: TypeScript | Next.js"
description: "Next.js provides a TypeScript-first development experience for building your React application."
weight: 1
type: docs
---

# TypeScript

Next.js provides a TypeScript-first development experience for building your React application.

It comes with built-in TypeScript support for automatically installing the necessary packages and configuring the proper settings.

## New Projects {#new-projects}

`create-next-app` now ships with TypeScript by default.


Terminal
```
npx create-next-app@latest
```

## Existing Projects {#existing-projects}

Add TypeScript to your project by renaming a file to `.ts` / `.tsx`. Run `next dev` and `next build` to automatically install the necessary dependencies and add a `tsconfig.json` file with the recommended config options.

If you already had a `jsconfig.json` file, copy the `paths` compiler option from the old `jsconfig.json` into the new `tsconfig.json` file, and delete the old `jsconfig.json` file.

## Minimum TypeScript Version {#minimum-typescript-version}

It is highly recommended to be on at least `v4.5.2` of TypeScript to get syntax features such as [type modifiers on import names ↗](https://devblogs.microsoft.com/typescript/announcing-typescript-4-5/#type-on-import-names) and [performance improvements ↗](https://devblogs.microsoft.com/typescript/announcing-typescript-4-5/#real-path-sync-native).

## Static Generation and Server-side Rendering {#static-generation-and-server-side-rendering}

For [`getStaticProps`](/nextjs/13.5/using-pages-router/api-reference/functions/get-static-props), [`getStaticPaths`](/nextjs/13.5/using-pages-router/api-reference/functions/get-static-paths), and [`getServerSideProps`](/nextjs/13.5/using-pages-router/api-reference/functions/get-server-side-props), you can use the `GetStaticProps`, `GetStaticPaths`, and `GetServerSideProps` types respectively:


pages/blog/[slug].tsx
```
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'
 
export const getStaticProps = (async (context) => {
  // ...
}) satisfies GetStaticProps
 
export const getStaticPaths = (async () => {
  // ...
}) satisfies GetStaticPaths
 
export const getServerSideProps = (async (context) => {
  // ...
}) satisfies GetServerSideProps
```

> **Good to know:**`satisfies` was added to TypeScript in [4.9 ↗](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html). We recommend upgrading to the latest version of TypeScript.
> 

## API Routes {#api-routes}

The following is an example of how to use the built-in types for API routes:

```
import type { NextApiRequest, NextApiResponse } from 'next'
 
export default function handler(req: NextApiRequest, res: NextApiResponse) {
  res.status(200).json({ name: 'John Doe' })
}
```

You can also type the response data:

```
import type { NextApiRequest, NextApiResponse } from 'next'
 
type Data = {
  name: string
}
 
export default function handler(
  req: NextApiRequest,
  res: NextApiResponse<Data>
) {
  res.status(200).json({ name: 'John Doe' })
}
```

## Custom `App` {#custom-app}

If you have a [custom `App`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-app), you can use the built-in type `AppProps` and change file name to `./pages/_app.tsx` like so:

```
import type { AppProps } from 'next/app'
 
export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

## Path aliases and baseUrl {#path-aliases-and-baseurl}

Next.js automatically supports the `tsconfig.json``"paths"` and `"baseUrl"` options.

You can learn more about this feature on the [Module Path aliases documentation](/nextjs/13.5/using-pages-router/building-your-application/configuring/absolute-imports-and-module-aliases).

## Type checking next.config.js {#type-checking-nextconfigjs}

The `next.config.js` file must be a JavaScript file as it does not get parsed by Babel or TypeScript, however you can add some type checking in your IDE using JSDoc as below:

```
// @ts-check
 
/**
 * @type {import('next').NextConfig}
 **/
const nextConfig = {
  /* config options here */
}
 
module.exports = nextConfig
```

## Incremental type checking {#incremental-type-checking}

Since `v10.2.1` Next.js supports [incremental type checking ↗](https://www.typescriptlang.org/tsconfig#incremental) when enabled in your `tsconfig.json`, this can help speed up type checking in larger applications.

## Ignoring TypeScript Errors {#ignoring-typescript-errors}

Next.js fails your **production build** (`next build`) when TypeScript errors are present in your project.

If you'd like Next.js to dangerously produce production code even when your application has errors, you can disable the built-in type checking step.

If disabled, be sure you are running type checks as part of your build or deploy process, otherwise this can be very dangerous.

Open `next.config.js` and enable the `ignoreBuildErrors` option in the `typescript` config:


next.config.js
```
module.exports = {
  typescript: {
    // !! WARN !!
    // Dangerously allow production builds to successfully complete even if
    // your project has type errors.
    // !! WARN !!
    ignoreBuildErrors: true,
  },
}
```

## Version Changes {#version-changes}

|Version|Changes|
|---|---|
|`v13.2.0`|Statically typed links are available in beta.|
|`v12.0.0`|[SWC](/nextjs/13.5/using-app-router/architecture/nextjs-compiler) is now used by default to compile TypeScript and TSX for faster builds.|
|`v10.2.1`|[Incremental type checking ↗](https://www.typescriptlang.org/tsconfig#incremental) support added when enabled in your `tsconfig.json`.|

