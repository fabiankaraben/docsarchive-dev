---
linkTitle: "revalidateTag"
title: "Functions: revalidateTag | Next.js"
description: "API Reference for the revalidateTag function."
weight: 15
type: docs
---

# revalidateTag

`revalidateTag` allows you to purge [cached data](/nextjs/13.5/using-app-router/building-your-application/caching) on-demand for a specific cache tag.

> **Good to know**:
> - `revalidateTag` is available in both [Node.js and Edge runtimes](/nextjs/13.5/using-app-router/building-your-application/rendering/edge-and-nodejs-runtimes).
> - `revalidateTag` only invalidates the cache when the path is next visited. This means calling `revalidateTag` with a dynamic route segment will not immediately trigger many revalidations at once. The invalidation only happens when the path is next visited.
> 

## Parameters {#parameters}

```
revalidateTag(tag: string): void;
```

- `tag`: A string representing the cache tag associated with the data you want to revalidate. Must be less than or equal to 256 characters.

You can add tags to `fetch` as follows:

```
fetch(url, { next: { tags: [...] } });
```

## Returns {#returns}

`revalidateTag` does not return any value.

## Examples {#examples}

### Server Action {#server-action}


app/actions.ts
```
'use server'
 
import { revalidateTag } from 'next/cache'
 
export default async function submit() {
  await addPost()
  revalidateTag('posts')
}
```

### Route Handler {#route-handler}


app/api/revalidate/route.ts
```
import { NextRequest, NextResponse } from 'next/server'
import { revalidateTag } from 'next/cache'
 
export async function GET(request: NextRequest) {
  const tag = request.nextUrl.searchParams.get('tag')
  revalidateTag(tag)
  return NextResponse.json({ revalidated: true, now: Date.now() })
}
```
