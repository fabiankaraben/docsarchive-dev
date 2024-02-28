---
linkTitle: "headers"
title: "Functions: headers | Next.js"
description: "API reference for the headers function."
weight: 7
type: docs
---

# headers

The `headers` function allows you to read the HTTP incoming request headers from a [Server Component ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html).

## `headers()` {#headers}

This API extends the [Web Headers API ↗](https://developer.mozilla.org/docs/Web/API/Headers). It is **read-only**, meaning you cannot `set` / `delete` the outgoing request headers.


app/page.tsx
```
import { headers } from 'next/headers'
 
export default function Page() {
  const headersList = headers()
  const referer = headersList.get('referer')
 
  return <div>Referer: {referer}</div>
}
```

> **Good to know**:
> - `headers()` is a **[Dynamic Function ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#server-rendering-strategies#dynamic-functions)** whose returned values cannot be known ahead of time. Using it in a layout or page will opt a route into **[dynamic rendering ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#dynamic-rendering)** at request time.
> 

### API Reference {#api-reference}

```
const headersList = headers()
```

#### Parameters {#parameters}

`headers` does not take any parameters.

#### Returns {#returns}

`headers` returns a **read-only**[Web Headers ↗](https://developer.mozilla.org/docs/Web/API/Headers) object.

- [`Headers.entries()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/entries): Returns an [`iterator` ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols) allowing to go through all key/value pairs contained in this object.
- [`Headers.forEach()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/forEach): Executes a provided function once for each key/value pair in this `Headers` object.
- [`Headers.get()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/get): Returns a [`String` ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) sequence of all the values of a header within a `Headers` object with a given name.
- [`Headers.has()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/has): Returns a boolean stating whether a `Headers` object contains a certain header.
- [`Headers.keys()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/keys): Returns an [`iterator` ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols) allowing you to go through all keys of the key/value pairs contained in this object.
- [`Headers.values()` ↗](https://developer.mozilla.org/docs/Web/API/Headers/values): Returns an [`iterator` ↗](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols) allowing you to go through all values of the key/value pairs contained in this object.

### Examples {#examples}

#### Usage with Data Fetching {#usage-with-data-fetching}

`headers()` can be used in combination with [Suspense for Data Fetching ↗](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html).


app/page.js
```
import { headers } from 'next/headers'
 
async function getUser() {
  const headersInstance = headers()
  const authorization = headersInstance.get('authorization')
  // Forward the authorization header
  const res = await fetch('...', {
    headers: { authorization },
  })
  return res.json()
}
 
export default async function UserPage() {
  const user = await getUser()
  return <h1>{user.name}</h1>
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`headers` introduced.|

