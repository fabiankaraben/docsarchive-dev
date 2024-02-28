---
linkTitle: "generateImageMetadata"
title: "Functions: generateImageMetadata | Next.js"
description: "Learn how to generate multiple images in a single Metadata API special file."
weight: 4
type: docs
---

# generateImageMetadata

You can use `generateImageMetadata` to generate different versions of one image or return multiple images for one route segment. This is useful for when you want to avoid hard-coding metadata values, such as for icons.

## Parameters {#parameters}

`generateImageMetadata` function accepts the following parameters:

#### `params` (optional) {#params-optional}

An object containing the [dynamic route parameters ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html) object from the root segment down to the segment `generateImageMetadata` is called from.


icon.tsx
```
export function generateImageMetadata({
  params,
}: {
  params: { slug: string }
}) {
  // ...
}
```

|Route|URL|`params`|
|---|---|---|
|`app/shop/icon.js`|`/shop`|`undefined`|
|`app/shop/[slug]/icon.js`|`/shop/1`|`{ slug: '1' }`|
|`app/shop/[tag]/[item]/icon.js`|`/shop/1/2`|`{ tag: '1', item: '2' }`|
|`app/shop/[...slug]/icon.js`|`/shop/1/2`|`{ slug: ['1', '2'] }`|


## Returns {#returns}

The `generateImageMetadata` function should return an `array` of objects containing the image's metadata such as `alt` and `size`. In addition, each item **must** include an `id` value will be passed to the props of the image generating function.

|Image Metadata Object|Type|
|---|---|
|`id`|`string` (required)|
|`alt`|`string`|
|`size`|`{ width: number; height: number }`|
|`contentType`|`string`|



icon.tsx
```
import { ImageResponse } from 'next/og'
 
export function generateImageMetadata() {
  return [
    {
      contentType: 'image/png',
      size: { width: 48, height: 48 },
      id: 'small',
    },
    {
      contentType: 'image/png',
      size: { width: 72, height: 72 },
      id: 'medium',
    },
  ]
}
 
export default function Icon({ id }: { id: string }) {
  return new ImageResponse(
    (
      <div
        style={{
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          fontSize: 88,
          background: '#000',
          color: '#fafafa',
        }}
      >
        Icon {id}
      </div>
    )
  )
}
```

### Examples {#examples}

#### Using external data {#using-external-data}

This example uses the `params` object and external data to generate multiple [Open Graph images](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image) for a route segment.


app/products/[id]/opengraph-image.tsx
```
import { ImageResponse } from 'next/og'
import { getCaptionForImage, getOGImages } from '@/app/utils/images'
 
export async function generateImageMetadata({
  params,
}: {
  params: { id: string }
}) {
  const images = await getOGImages(params.id)
 
  return images.map((image, idx) => ({
    id: idx,
    size: { width: 1200, height: 600 },
    alt: image.text,
    contentType: 'image/png',
  }))
}
 
export default async function Image({
  params,
  id,
}: {
  params: { id: string }
  id: number
}) {
  const productId = params.id
  const imageId = id
  const text = await getCaptionForImage(productId, imageId)
 
  return new ImageResponse(
    (
      <div
        style={
          {
            // ...
          }
        }
      >
        {text}
      </div>
    )
  )
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|`generateImageMetadata` introduced.|

