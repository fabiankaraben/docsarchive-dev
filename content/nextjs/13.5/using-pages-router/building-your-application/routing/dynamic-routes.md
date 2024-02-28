---
linkTitle: "Dynamic Routes"
title: "Routing: Dynamic Routes | Next.js"
description: "Dynamic Routes are pages that allow you to add custom params to your URLs. Start creating Dynamic Routes and learn more here."
weight: 2
type: docs
---

# Dynamic Routes

When you don't know the exact segment names ahead of time and want to create routes from dynamic data, you can use Dynamic Segments that are filled in at request time or [prerendered](/nextjs/13.5/using-pages-router/building-your-application/data-fetching/get-static-paths) at build time.

## Convention {#convention}

A Dynamic Segment can be created by wrapping a file or folder name in square brackets: `[segmentName]`. For example, `[id]` or `[slug]`.

Dynamic Segments can be accessed from [`useRouter`](/nextjs/13.5/using-pages-router/api-reference/functions/use-router).

## Example {#example}

For example, a blog could include the following route `pages/blog/[slug].js` where `[slug]` is the Dynamic Segment for blog posts.

```
import { useRouter } from 'next/router'
 
export default function Page() {
  const router = useRouter()
  return <p>Post: {router.query.slug}</p>
}
```

|Route|Example URL|`params`|
|---|---|---|
|`pages/blog/[slug].js`|`/blog/a`|`{ slug: 'a' }`|
|`pages/blog/[slug].js`|`/blog/b`|`{ slug: 'b' }`|
|`pages/blog/[slug].js`|`/blog/c`|`{ slug: 'c' }`|


## Catch-all Segments {#catch-all-segments}

Dynamic Segments can be extended to **catch-all** subsequent segments by adding an ellipsis inside the brackets `[...segmentName]`.

For example, `pages/shop/[...slug].js` will match `/shop/clothes`, but also `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`, and so on.

|Route|Example URL|`params`|
|---|---|---|
|`pages/shop/[...slug].js`|`/shop/a`|`{ slug: ['a'] }`|
|`pages/shop/[...slug].js`|`/shop/a/b`|`{ slug: ['a', 'b'] }`|
|`pages/shop/[...slug].js`|`/shop/a/b/c`|`{ slug: ['a', 'b', 'c'] }`|


## Optional Catch-all Segments {#optional-catch-all-segments}

Catch-all Segments can be made **optional** by including the parameter in double square brackets: `[[...segmentName]]`.

For example, `pages/shop/[[...slug]].js` will **also** match `/shop`, in addition to `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`.

The difference between **catch-all** and **optional catch-all** segments is that with optional, the route without the parameter is also matched (`/shop` in the example above).

|Route|Example URL|`params`|
|---|---|---|
|`pages/shop/[[...slug]].js`|`/shop`|`{}`|
|`pages/shop/[[...slug]].js`|`/shop/a`|`{ slug: ['a'] }`|
|`pages/shop/[[...slug]].js`|`/shop/a/b`|`{ slug: ['a', 'b'] }`|
|`pages/shop/[[...slug]].js`|`/shop/a/b/c`|`{ slug: ['a', 'b', 'c'] }`|

