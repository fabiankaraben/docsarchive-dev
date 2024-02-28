---
linkTitle: "revalidatePath"
title: "Functions: revalidatePath | Next.js"
description: "API Reference for the revalidatePath function."
weight: 14
type: docs
---

# revalidatePath

`revalidatePath` allows you to purge [cached data](/nextjs/13.5/using-app-router/building-your-application/caching) on-demand for a specific path.

> **Good to know**:
> - `revalidatePath` is available in both [Node.js and Edge runtimes](/nextjs/13.5/using-app-router/building-your-application/rendering/edge-and-nodejs-runtimes).
> - `revalidatePath` only invalidates the cache when the included path is next visited. This means calling `revalidatePath` with a dynamic route segment will not immediately trigger many revalidations at once. The invalidation only happens when the path is next visited.
> 

## Parameters {#parameters}

```
revalidatePath(path: string, type?: 'page' | 'layout'): void;
```

- `path`: A string representing the filesystem path associated with the data you want to revalidate. This is the literal route segment (for example, `/product/123`) not the path on the filesystem `/product/[slug]/page`. Must be less than 1024 characters.
- `type`: (optional) `'page'` or `'layout'` string to change the type of path to revalidate.

## Returns {#returns}

`revalidatePath` does not return any value.

## Examples {#examples}

### Revalidating A Specific URL {#revalidating-a-specific-url}

```
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/post-1')
```

This will revalidate one specific URL on the next page visit.

### Revalidating A Page Path {#revalidating-a-page-path}

```
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/[slug]', 'page')
// or with route groups
revalidatePath('/(main)/post/[slug]', 'page')
```

This will revalidate any URL that matches the provided `page` file on the next page visit. This will *not* invalidate pages beneath the specific page. For example, `/blog/[slug]` won't invalidate `/blog/[slug]/[author]`.

### Revalidating A Layout Path {#revalidating-a-layout-path}

```
import { revalidatePath } from 'next/cache'
revalidatePath('/blog/[slug]', 'layout')
// or with route groups
revalidatePath('/(main)/post/[slug]', 'layout')
```

This will revalidate any URL that matches the provided `layout` file on the next page visit. This will cause pages beneath with the same layout to revalidate on the next visit. For example, in the above case, `/blog/[slug]/[another]` would also revalidate on the next visit.

### Server Action {#server-action}


app/actions.ts
```
'use server'
 
import { revalidatePath } from 'next/cache'
 
export default async function submit() {
  await submitForm()
  revalidatePath('/')
}
```

### Route Handler {#route-handler}


app/api/revalidate/route.ts
```
import { revalidatePath } from 'next/cache'
import { NextRequest, NextResponse } from 'next/server'
 
export async function GET(request: NextRequest) {
  const path = request.nextUrl.searchParams.get('path')
 
  if (path) {
    revalidatePath(path)
    return NextResponse.json({ revalidated: true, now: Date.now() })
  }
 
  return NextResponse.json({
    revalidated: false,
    now: Date.now(),
    message: 'Missing path to revalidate',
  })
}
```
