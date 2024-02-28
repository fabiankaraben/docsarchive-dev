---
linkTitle: "opengraph-image and twitter-image"
title: "Metadata Files: opengraph-image and twitter-image | Next.js"
description: "API Reference for the Open Graph Image and Twitter Image file conventions."
weight: 3
type: docs
---

# opengraph-image and twitter-image

The `opengraph-image` and `twitter-image` file conventions allow you to set Open Graph and Twitter images for a route segment.

They are useful for setting the images that appear on social networks and messaging apps when a user shares a link to your site.

There are two ways to set Open Graph and Twitter images:

- [Using image files (.jpg, .png, .gif)](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#image-files-jpg-png-gif)
- [Using code to generate images (.js, .ts, .tsx)](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#generate-images-using-code-js-ts-tsx)

## Image files (.jpg, .png, .gif) {#image-files-jpg-png-gif}

Use an image file to set a route segment's shared image by placing an `opengraph-image` or `twitter-image` image file in the segment.

Next.js will evaluate the file and automatically add the appropriate tags to your app's `<head>` element.

|File convention|Supported file types|
|---|---|
|[`opengraph-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#opengraph-image)|`.jpg`, `.jpeg`, `.png`, `.gif`|
|[`twitter-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#twitter-image)|`.jpg`, `.jpeg`, `.png`, `.gif`|
|[`opengraph-image.alt`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#opengraph-imagealttxt)|`.txt`|
|[`twitter-image.alt`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#twitter-imagealttxt)|`.txt`|


### `opengraph-image` {#opengraph-image}

Add an `opengraph-image.(jpg|jpeg|png|gif)` image file to any route segment.


<head> output
```
<meta property="og:image" content="<generated>" />
<meta property="og:image:type" content="<generated>" />
<meta property="og:image:width" content="<generated>" />
<meta property="og:image:height" content="<generated>" />
```

### `twitter-image` {#twitter-image}

Add a `twitter-image.(jpg|jpeg|png|gif)` image file to any route segment.


<head> output
```
<meta name="twitter:image" content="<generated>" />
<meta name="twitter:image:type" content="<generated>" />
<meta name="twitter:image:width" content="<generated>" />
<meta name="twitter:image:height" content="<generated>" />
```

### `opengraph-image.alt.txt` {#opengraph-imagealttxt}

Add an accompanying `opengraph-image.alt.txt` file in the same route segment as the `opengraph-image.(jpg|jpeg|png|gif)` image it's alt text.


opengraph-image.alt.txt
```
About Acme
```


<head> output
```
<meta property="og:image:alt" content="About Acme" />
```

### `twitter-image.alt.txt` {#twitter-imagealttxt}

Add an accompanying `twitter-image.alt.txt` file in the same route segment as the `twitter-image.(jpg|jpeg|png|gif)` image it's alt text.


twitter-image.alt.txt
```
About Acme
```


<head> output
```
<meta property="twitter:image:alt" content="About Acme" />
```

## Generate images using code (.js, .ts, .tsx) {#generate-images-using-code-js-ts-tsx}

In addition to using [literal image files](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#image-files-jpg-png-gif), you can programmatically **generate** images using code.

Generate a route segment's shared image by creating an `opengraph-image` or `twitter-image` route that default exports a function.

|File convention|Supported file types|
|---|---|
|`opengraph-image`|`.js`, `.ts`, `.tsx`|
|`twitter-image`|`.js`, `.ts`, `.tsx`|


> **Good to know**
> - By default, generated images are [**statically optimized** ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#static-rendering-default) (generated at build time and cached) unless they use [dynamic functions ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#server-rendering-strategies#dynamic-functions) or uncached data.
> - You can generate multiple Images in the same file using [`generateImageMetadata`](/nextjs/13.5/using-app-router/api-reference/functions/generate-image-metadata).
> 

The easiest way to generate an image is to use the [ImageResponse](/nextjs/13.5/using-app-router/api-reference/functions/image-response) API from `next/server`.


app/about/opengraph-image.tsx
```
import { ImageResponse } from 'next/server'
 
// Route segment config
export const runtime = 'edge'
 
// Image metadata
export const alt = 'About Acme'
export const size = {
  width: 1200,
  height: 630,
}
 
export const contentType = 'image/png'
 
// Image generation
export default async function Image() {
  // Font
  const interSemiBold = fetch(
    new URL('./Inter-SemiBold.ttf', import.meta.url)
  ).then((res) => res.arrayBuffer())
 
  return new ImageResponse(
    (
      // ImageResponse JSX element
      <div
        style={{
          fontSize: 128,
          background: 'white',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        About Acme
      </div>
    ),
    // ImageResponse options
    {
      // For convenience, we can re-use the exported opengraph-image
      // size config to also set the ImageResponse's width and height.
      ...size,
      fonts: [
        {
          name: 'Inter',
          data: await interSemiBold,
          style: 'normal',
          weight: 400,
        },
      ],
    }
  )
}
```


<head> output
```
<meta property="og:image" content="<generated>" />
<meta property="og:image:alt" content="About Acme" />
<meta property="og:image:type" content="image/png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
```

### Props {#props}

The default export function receives the following props:

#### `params` (optional) {#params-optional}

An object containing the [dynamic route parameters ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html) object from the root segment down to the segment `opengraph-image` or `twitter-image` is colocated in.


app/shop/[slug]/opengraph-image.tsx
```
export default function Image({ params }: { params: { slug: string } }) {
  // ...
}
```

|Route|URL|`params`|
|---|---|---|
|`app/shop/opengraph-image.js`|`/shop`|`undefined`|
|`app/shop/[slug]/opengraph-image.js`|`/shop/1`|`{ slug: '1' }`|
|`app/shop/[tag]/[item]/opengraph-image.js`|`/shop/1/2`|`{ tag: '1', item: '2' }`|
|`app/shop/[...slug]/opengraph-image.js`|`/shop/1/2`|`{ slug: ['1', '2'] }`|


### Returns {#returns}

The default export function should return a `Blob` | `ArrayBuffer` | `TypedArray` | `DataView` | `ReadableStream` | `Response`.

> **Good to know**: `ImageResponse` satisfies this return type.
> 

### Config exports {#config-exports}

You can optionally configure the image's metadata by exporting `alt`, `size`, and `contentType` variables from `opengraph-image` or `twitter-image` route.

|Option|Type|
|---|---|
|[`alt`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#alt)|`string`|
|[`size`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#size)|`{ width: number; height: number }`|
|[`contentType`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#contenttype)|`string` - [image MIME type ↗](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types#image_types)|


#### `alt` {#alt}


opengraph-image.tsx / twitter-image.tsx
```
export const alt = 'My images alt text'
 
export default function Image() {}
```


<head> output
```
<meta property="og:image:alt" content="My images alt text" />
```

#### `size` {#size}


opengraph-image.tsx / twitter-image.tsx
```
export const size = { width: 1200, height: 630 }
 
export default function Image() {}
```


<head> output
```
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
```

#### `contentType` {#contenttype}


opengraph-image.tsx / twitter-image.tsx
```
export const contentType = 'image/png'
 
export default function Image() {}
```


<head> output
```
<meta property="og:image:type" content="image/png" />
```

#### Route Segment Config {#route-segment-config}

`opengraph-image` and `twitter-image` are specialized [Route Handlers](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers) that can use the same [route segment configuration ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html) options as Pages and Layouts.

|Option|Type|Default|
|---|---|---|
|[`dynamic` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#dynamic)|`'auto' | 'force-dynamic' | 'error' | 'force-static'`|`'auto'`|
|[`revalidate` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#revalidate)|`false | 'force-cache' | 0 | number`|`false`|
|[`runtime` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#runtime)|`'nodejs' | 'edge'`|`'nodejs'`|
|[`preferredRegion` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#preferredregion)|`'auto' | 'global' | 'home' | string | string[]`|`'auto'`|



app/opengraph-image.tsx
```
export const runtime = 'edge'
 
export default function Image() {}
```

### Examples {#examples}

#### Using external data {#using-external-data}

This example uses the `params` object and external data to generate the image.

> **Good to know**:
> By default, this generated image will be [statically optimized ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#static-rendering-default). You can configure the individual `fetch`[`options` ↗](https://nextjs.org/docs/app/api-reference/functions/fetch.html) or route segments [options ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#revalidate) to change this behavior.
> 


app/posts/[slug]/opengraph-image.tsx
```
import { ImageResponse } from 'next/server'
 
export const runtime = 'edge'
 
export const alt = 'About Acme'
export const size = {
  width: 1200,
  height: 630,
}
export const contentType = 'image/png'
 
export default async function Image({ params }: { params: { slug: string } }) {
  const post = await fetch(`https://.../posts/${params.slug}`).then((res) =>
    res.json()
  )
 
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 48,
          background: 'white',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        {post.title}
      </div>
    ),
    {
      ...size,
    }
  )
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|`opengraph-image` and `twitter-image` introduced.|
