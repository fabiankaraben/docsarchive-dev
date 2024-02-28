---
linkTitle: "NextRequest"
title: "Functions: NextRequest | Next.js"
description: "API Reference for NextRequest."
weight: 9
type: docs
---

# NextRequest

NextRequest extends the [Web Request API ↗](https://developer.mozilla.org/docs/Web/API/Request) with additional convenience methods.

## `cookies` {#cookies}

Read or mutate the [`Set-Cookie` ↗](https://developer.mozilla.org/docs/Web/HTTP/Headers/Set-Cookie) header of the request.

### `set(name, value)` {#setname-value}

Given a name, set a cookie with the given value on the request.

```
// Given incoming request /home
// Set a cookie to hide the banner
// request will have a `Set-Cookie:show-banner=false;path=/home` header
request.cookies.set('show-banner', 'false')
```

### `get(name)` {#getname}

Given a cookie name, return the value of the cookie. If the cookie is not found, `undefined` is returned. If multiple cookies are found, the first one is returned.

```
// Given incoming request /home
// { name: 'show-banner', value: 'false', Path: '/home' }
request.cookies.get('show-banner')
```

### `getAll()` {#getall}

Given a cookie name, return the values of the cookie. If no name is given, return all cookies on the request.

```
// Given incoming request /home
// [
//   { name: 'experiments', value: 'new-pricing-page', Path: '/home' },
//   { name: 'experiments', value: 'winter-launch', Path: '/home' },
// ]
request.cookies.getAll('experiments')
// Alternatively, get all cookies for the request
request.cookies.getAll()
```

### `delete(name)` {#deletename}

Given a cookie name, delete the cookie from the request.

```
// Returns true for deleted, false is nothing is deleted
request.cookies.delete('experiments')
```

### `has(name)` {#hasname}

Given a cookie name, return `true` if the cookie exists on the request.

```
// Returns true if cookie exists, false if it does not
request.cookies.has('experiments')
```

### `clear()` {#clear}

Remove the `Set-Cookie` header from the request.

```
request.cookies.clear()
```

## `nextUrl` {#nexturl}

Extends the native [`URL` ↗](https://developer.mozilla.org/docs/Web/API/URL) API with additional convenience methods, including Next.js specific properties.

```
// Given a request to /home, pathname is /home
request.nextUrl.pathname
// Given a request to /home?name=lee, searchParams is { 'name': 'lee' }
request.nextUrl.searchParams
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`useSearchParams` introduced.|

