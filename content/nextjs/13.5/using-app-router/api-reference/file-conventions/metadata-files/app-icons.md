---
linkTitle: "favicon, icon, and apple-icon"
title: "Metadata Files: favicon, icon, and apple-icon | Next.js"
description: "API Reference for the Favicon, Icon and Apple Icon file conventions."
weight: 1
type: docs
---

# favicon, icon, and apple-icon

The `favicon`, `icon`, or `apple-icon` file conventions allow you to set icons for your application.

They are useful for adding app icons that appear in places like web browser tabs, phone home screens, and search engine results.

There are two ways to set app icons:

- [Using image files (.ico, .jpg, .png)](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#image-files-ico-jpg-png)
- [Using code to generate an icon (.js, .ts, .tsx)](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#generate-icons-using-code-js-ts-tsx)

## Image files (.ico, .jpg, .png) {#image-files-ico-jpg-png}

Use an image file to set an app icon by placing a `favicon`, `icon`, or `apple-icon` image file within your `/app` directory.
The `favicon` image can only be located in the top level of `app/`.

Next.js will evaluate the file and automatically add the appropriate tags to your app's `<head>` element.

|File convention|Supported file types|Valid locations|
|---|---|---|
|[`favicon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#favicon)|`.ico`|`app/`|
|[`icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#icon)|`.ico`, `.jpg`, `.jpeg`, `.png`, `.svg`|`app/**/*`|
|[`apple-icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#apple-icon)|`.jpg`, `.jpeg`, `.png`|`app/**/*`|


### `favicon` {#favicon}

Add a `favicon.ico` image file to the root `/app` route segment.


<head> output
```
<link rel="icon" href="/favicon.ico" sizes="any" />
```

### `icon` {#icon}

Add an `icon.(ico|jpg|jpeg|png|svg)` image file.


<head> output
```
<link
  rel="icon"
  href="/icon?<generated>"
  type="image/<generated>"
  sizes="<generated>"
/>
```

### `apple-icon` {#apple-icon}

Add an `apple-icon.(jpg|jpeg|png)` image file.


<head> output
```
<link
  rel="apple-touch-icon"
  href="/apple-icon?<generated>"
  type="image/<generated>"
  sizes="<generated>"
/>
```

> **Good to know**
> - You can set multiple icons by adding a number suffix to the file name. For example, `icon1.png`, `icon2.png`, etc. Numbered files will sort lexically.
> - Favicons can only be set in the root `/app` segment. If you need more granularity, you can use [`icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#icon).
> - The appropriate `<link>` tags and attributes such as `rel`, `href`, `type`, and `sizes` are determined by the icon type and metadata of the evaluated file.- For example, a 32 by 32px `.png` file will have `type="image/png"` and `sizes="32x32"` attributes.
> - `sizes="any"` is added to `favicon.ico` output to [avoid a browser bug ↗](https://evilmartians.com/chronicles/how-to-favicon-in-2021-six-files-that-fit-most-needs) where an `.ico` icon is favored over `.svg`.
> 

## Generate icons using code (.js, .ts, .tsx) {#generate-icons-using-code-js-ts-tsx}

In addition to using [literal image files](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#image-files-ico-jpg-png), you can programmatically **generate** icons using code.

Generate an app icon by creating an `icon` or `apple-icon` route that default exports a function.

|File convention|Supported file types|
|---|---|
|`icon`|`.js`, `.ts`, `.tsx`|
|`apple-icon`|`.js`, `.ts`, `.tsx`|


The easiest way to generate an icon is to use the [`ImageResponse`](/nextjs/13.5/using-app-router/api-reference/functions/image-response) API from `next/og`.


app/icon.tsx
```
import { ImageResponse } from 'next/og'
 
// Route segment config
export const runtime = 'edge'
 
// Image metadata
export const size = {
  width: 32,
  height: 32,
}
export const contentType = 'image/png'
 
// Image generation
export default function Icon() {
  return new ImageResponse(
    (
      // ImageResponse JSX element
      <div
        style={{
          fontSize: 24,
          background: 'black',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          color: 'white',
        }}
      >
        A
      </div>
    ),
    // ImageResponse options
    {
      // For convenience, we can re-use the exported icons size metadata
      // config to also set the ImageResponse's width and height.
      ...size,
    }
  )
}
```


<head> output
```
<link rel="icon" href="/icon?<generated>" type="image/png" sizes="32x32" />
```

> **Good to know**
> - By default, generated icons are [**statically optimized** ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#static-rendering-default) (generated at build time and cached) unless they use [dynamic functions ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html#server-rendering-strategies#dynamic-functions) or uncached data.
> - You can generate multiple icons in the same file using [`generateImageMetadata`](/nextjs/13.5/using-app-router/api-reference/functions/generate-image-metadata).
> - You cannot generate a `favicon` icon. Use [`icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#icon) or a [favicon.ico](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#favicon) file instead.
> 

### Props {#props}

The default export function receives the following props:

#### `params` (optional) {#params-optional}

An object containing the [dynamic route parameters ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html) object from the root segment down to the segment `icon` or `apple-icon` is colocated in.


app/shop/[slug]/icon.tsx
```
export default function Icon({ params }: { params: { slug: string } }) {
  // ...
}
```

|Route|URL|`params`|
|---|---|---|
|`app/shop/icon.js`|`/shop`|`undefined`|
|`app/shop/[slug]/icon.js`|`/shop/1`|`{ slug: '1' }`|
|`app/shop/[tag]/[item]/icon.js`|`/shop/1/2`|`{ tag: '1', item: '2' }`|
|`app/shop/[...slug]/icon.js`|`/shop/1/2`|`{ slug: ['1', '2'] }`|


### Returns {#returns}

The default export function should return a `Blob` | `ArrayBuffer` | `TypedArray` | `DataView` | `ReadableStream` | `Response`.

> **Good to know**: `ImageResponse` satisfies this return type.
> 

### Config exports {#config-exports}

You can optionally configure the icon's metadata by exporting `size` and `contentType` variables from the `icon` or `apple-icon` route.

|Option|Type|
|---|---|
|[`size`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#size)|`{ width: number; height: number }`|
|[`contentType`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#contenttype)|`string` - [image MIME type ↗](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types#image_types)|


#### `size` {#size}


icon.tsx | apple-icon.tsx
```
export const size = { width: 32, height: 32 }
 
export default function Icon() {}
```


<head> output
```
<link rel="icon" sizes="32x32" />
```

#### `contentType` {#contenttype}


icon.tsx | apple-icon.tsx
```
export const contentType = 'image/png'
 
export default function Icon() {}
```


<head> output
```
<link rel="icon" type="image/png" />
```

#### Route Segment Config {#route-segment-config}

`icon` and `apple-icon` are specialized [Route Handlers](/nextjs/13.5/using-app-router/building-your-application/routing/route-handlers) that can use the same [route segment configuration ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html) options as Pages and Layouts.

|Option|Type|Default|
|---|---|---|
|[`dynamic` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#dynamic)|`'auto' | 'force-dynamic' | 'error' | 'force-static'`|`'auto'`|
|[`revalidate` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#revalidate)|`false | 'force-cache' | 0 | number`|`false`|
|[`runtime` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#runtime)|`'nodejs' | 'edge'`|`'nodejs'`|
|[`preferredRegion` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config.html#preferredregion)|`'auto' | 'global' | 'home' | string | string[]`|`'auto'`|



app/icon.tsx
```
export const runtime = 'edge'
 
export default function Icon() {}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|`favicon``icon` and `apple-icon` introduced|

