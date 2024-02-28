---
linkTitle: "not-found.js"
title: "File Conventions: not-found.js | Next.js"
description: "API reference for the not-found.js file."
weight: 5
type: docs
---

# not-found.js

The **not-found** file is used to render UI when the [`notFound`](/nextjs/13.5/using-app-router/api-reference/functions/not-found) function is thrown within a route segment. Along with serving a custom UI, Next.js will return a `200` HTTP status code for streamed responses, and `404` for non-streamed responses.


app/blog/not-found.tsx
```
import Link from 'next/link'
 
export default function NotFound() {
  return (
    <div>
      <h2>Not Found</h2>
      <p>Could not find requested resource</p>
      <Link href="/">Return Home</Link>
    </div>
  )
}
```

> **Good to know**: In addition to catching expected `notFound()` errors, the root `app/not-found.js` file also handles any unmatched URLs for your whole application. This means users that visit a URL that is not handled by your app will be shown the UI exported by the `app/not-found.js` file.
> 

## Props {#props}

`not-found.js` components do not accept any props.

## Data Fetching {#data-fetching}

By default, `not-found` is a Server Component. You can mark it as `async` to fetch and display data:


app/blog/not-found.tsx
```
import Link from 'next/link'
import { headers } from 'next/headers'
 
export default async function NotFound() {
  const headersList = headers()
  const domain = headersList.get('host')
  const data = await getSiteData(domain)
  return (
    <div>
      <h2>Not Found: {data.name}</h2>
      <p>Could not find requested resource</p>
      <p>
        View <Link href="/blog">all posts</Link>
      </p>
    </div>
  )
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|Root `app/not-found` handles global unmatched URLs.|
|`v13.0.0`|`not-found` introduced.|

