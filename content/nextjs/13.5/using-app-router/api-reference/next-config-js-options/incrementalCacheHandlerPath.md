---
linkTitle: "incrementalCacheHandlerPath"
title: "next.config.js Options: incrementalCacheHandlerPath | Next.js"
description: "Configure the Next.js cache used for storing and revalidating data."
weight: 15
type: docs
---

# incrementalCacheHandlerPath

In Next.js, the [default cache handler ↗](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html) uses the filesystem cache. This requires no configuration, however, you can customize the cache handler by using the `incrementalCacheHandlerPath` field in `next.config.js`.


next.config.js
```
module.exports = {
  experimental: {
    incrementalCacheHandlerPath: require.resolve('./cache-handler.js'),
  },
}
```

Here's an example of a custom cache handler:


cache-handler.js
```
const cache = new Map()
 
module.exports = class CacheHandler {
  constructor(options) {
    this.options = options
    this.cache = {}
  }
 
  async get(key) {
    return cache.get(key)
  }
 
  async set(key, data) {
    cache.set(key, {
      value: data,
      lastModified: Date.now(),
    })
  }
}
```

## API Reference {#api-reference}

The cache handler can implement the following methods: `get`, `set`, and `revalidateTag`.

### `get()` {#get}

|Parameter|Type|Description|
|---|---|---|
|`key`|`string`|The key to the cached value.|


Returns the cached value or `null` if not found.

### `set()` {#set}

|Parameter|Type|Description|
|---|---|---|
|`key`|`string`|The key to store the data under.|
|`data`|Data or `null`|The data to be cached.|


Returns `Promise<void>`.

### `revalidateTag()` {#revalidatetag}

|Parameter|Type|Description|
|---|---|---|
|`tag`|`string`|The cache tag to revalidate.|


Returns `Promise<void>`. Learn more about [revalidating data ↗](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html) or the [`revalidateTag()`](/nextjs/13.5/using-app-router/api-reference/functions/revalidateTag) function.
