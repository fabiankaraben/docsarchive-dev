---
linkTitle: "cookies"
title: "Functions: cookies | Next.js"
description: "API Reference for the cookies function."
weight: 1
type: docs
---

# cookies

The `cookies` function allows you to read the HTTP incoming request cookies from a [Server Component ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html) or write outgoing request cookies in a [Server Action](/nextjs/13.5/using-app-router/building-your-application/data-fetching/forms-and-mutations) or [Route Handler](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers).

> **Good to know**: `cookies()` is a **[Dynamic Function ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#server-rendering-strategies#dynamic-functions)** whose returned values cannot be known ahead of time. Using it in a layout or page will opt a route into **[dynamic rendering ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#dynamic-rendering)** at request time.
> 

## `cookies().get(name)` {#cookiesgetname}

A method that takes a cookie name and returns an object with name and value. If a cookie with `name` isn't found, it returns `undefined`. If multiple cookies match, it will only return the first match.


app/page.js
```
import { cookies } from 'next/headers'
 
export default function Page() {
  const cookieStore = cookies()
  const theme = cookieStore.get('theme')
  return '...'
}
```

## `cookies().getAll()` {#cookiesgetall}

A method that is similar to `get`, but returns a list of all the cookies with a matching `name`. If `name` is unspecified, it returns all the available cookies.


app/page.js
```
import { cookies } from 'next/headers'
 
export default function Page() {
  const cookieStore = cookies()
  return cookieStore.getAll().map((cookie) => (
    <div key={cookie.name}>
      <p>Name: {cookie.name}</p>
      <p>Value: {cookie.value}</p>
    </div>
  ))
}
```

## `cookies().has(name)` {#cookieshasname}

A method that takes a cookie name and returns a `boolean` based on if the cookie exists (`true`) or not (`false`).


app/page.js
```
import { cookies } from 'next/headers'
 
export default function Page() {
  const cookiesList = cookies()
  const hasCookie = cookiesList.has('theme')
  return '...'
}
```

## `cookies().set(name, value, options)` {#cookiessetname-value-options}

A method that takes a cookie name, value, and options and sets the outgoing request cookie.

> **Good to know**: HTTP does not allow setting cookies after streaming starts, so you must use `.set()` in a [Server Action](/nextjs/13.5/using-app-router/building-your-application/data-fetching/forms-and-mutations) or [Route Handler](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers).
> 


app/actions.js
```
'use server'
 
import { cookies } from 'next/headers'
 
async function create(data) {
  cookies().set('name', 'lee')
  // or
  cookies().set('name', 'lee', { secure: true })
  // or
  cookies().set({
    name: 'name',
    value: 'lee',
    httpOnly: true,
    path: '/',
  })
}
```

## Deleting cookies {#deleting-cookies}

> **Good to know**: You can only delete cookies in a [Server Action](/nextjs/13.5/using-app-router/building-your-application/data-fetching/forms-and-mutations) or [Route Handler](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers).
> 

There are several options for deleting a cookie:

### `cookies().delete(name)` {#cookiesdeletename}

You can explicitly delete a cookie with a given name.


app/actions.js
```
'use server'
 
import { cookies } from 'next/headers'
 
async function create(data) {
  cookies().delete('name')
}
```

### `cookies().set(name, '')` {#cookiessetname-}

Alternatively, you can set a new cookie with the same name and an empty value.


app/actions.js
```
'use server'
 
import { cookies } from 'next/headers'
 
async function create(data) {
  cookies().set('name', '')
}
```

> **Good to know**: `.set()` is only available in a [Server Action](/nextjs/13.5/using-app-router/building-your-application/data-fetching/forms-and-mutations) or [Route Handler](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers).
> 

### `cookies().set(name, value, { maxAge: 0 })` {#cookiessetname-value--maxage-0-}

Setting `maxAge` to 0 will immediately expire a cookie.


app/actions.js
```
'use server'
 
import { cookies } from 'next/headers'
 
async function create(data) {
  cookies().set('name', 'value', { maxAge: 0 })
}
```

### `cookies().set(name, value, { expires: timestamp })` {#cookiessetname-value--expires-timestamp-}

Setting `expires` to any value in the past will immediately expire a cookie.


app/actions.js
```
'use server'
 
import { cookies } from 'next/headers'
 
async function create(data) {
  const oneDay = 24 * 60 * 60 * 1000
  cookies().set('name', 'value', { expires: Date.now() - oneDay })
}
```

> **Good to know**: You can only delete cookies that belong to the same domain from which `.set()` is called. Additionally, the code must be executed on the same protocol (HTTP or HTTPS) as the cookie you want to delete.
> 

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`cookies` introduced.|

