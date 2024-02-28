---
linkTitle: "loading.js"
title: "File Conventions: loading.js | Next.js"
description: "API reference for the loading.js  file."
weight: 4
type: docs
---

# loading.js

A **loading** file can create instant loading states built on [Suspense](/nextjs/13.5/using-app-router/building-your-application/routing/loading-ui-and-streaming).

By default, this file is a [Server Component ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html) - but can also be used as a Client Component through the `"use client"` directive.


app/feed/loading.tsx
```
export default function Loading() {
  // Or a custom loading skeleton component
  return <p>Loading...</p>
}
```

Loading UI components do not accept any parameters.

> **Good to know**
> - While designing loading UI, you may find it helpful to use the [React Developer Tools ↗](https://react.dev/learn/react-developer-tools) to manually toggle Suspense boundaries.
> 

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`loading` introduced.|

