---
linkTitle: "notFound"
title: "Functions: notFound | Next.js"
description: "API Reference for the notFound function."
weight: 11
type: docs
---

# notFound

The `notFound` function allows you to render the [`not-found file`](/nextjs/13.5/using-app-router/api-reference/file-conventions/not-found) within a route segment as well as inject a `<meta name="robots" content="noindex" />` tag.

## `notFound()` {#notfound}

Invoking the `notFound()` function throws a `NEXT_NOT_FOUND` error and terminates rendering of the route segment in which it was thrown. Specifying a [**not-found** file](/nextjs/13.5/using-app-router/api-reference/file-conventions/not-found) allows you to gracefully handle such errors by rendering a Not Found UI within the segment.


app/user/[id]/page.js
```
import { notFound } from 'next/navigation'
 
async function fetchUser(id) {
  const res = await fetch('https://...')
  if (!res.ok) return undefined
  return res.json()
}
 
export default async function Profile({ params }) {
  const user = await fetchUser(params.id)
 
  if (!user) {
    notFound()
  }
 
  // ...
}
```

> **Good to know**: `notFound()` does not require you to use `return notFound()` due to using the TypeScript [`never` ↗](https://www.typescriptlang.org/docs/handbook/2/functions.html#never) type.
> 

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`notFound` introduced.|

