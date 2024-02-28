---
linkTitle: "Static Assets"
title: "Optimizing: Static Assets | Next.js"
description: "Next.js allows you to serve static files, like images, in the public directory. You can learn how it works here."
weight: 5
type: docs
---

# Static Assets

Next.js can serve static files, like images, under a folder called `public` in the root directory. Files inside `public` can then be referenced by your code starting from the base URL (`/`).

For example, if you add `me.png` inside `public`, the following code will access the image:


Avatar.js
```
import Image from 'next/image'
 
export function Avatar() {
  return <Image src="/me.png" alt="me" width="64" height="64" />
}
```

For static metadata files, such as `robots.txt`, `favicon.ico`, etc, you should use [special metadata files ↗](https://nextjs.org/docs/app/api-reference/file-conventions/metadata.html) inside the `app` folder.

> Good to know:
> - The directory must be named `public`. The name cannot be changed and it's the only directory used to serve static assets.
> - Only assets that are in the `public` directory at [build time](/nextjs/13.5/using-app-router/api-reference/next-cli#build) will be served by Next.js. Files added at request time won't be available. We recommend using a third-party service like [AWS S3 ↗](https://aws.amazon.com/s3/) for persistent file storage.
> 
