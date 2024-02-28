---
linkTitle: "robots.txt"
title: "Metadata Files: robots.txt | Next.js"
description: "API Reference for robots.txt file."
weight: 4
type: docs
---

# robots.txt

Add or generate a `robots.txt` file that matches the [Robots Exclusion Standard ↗](https://en.wikipedia.org/wiki/Robots.txt#Standard) in the **root** of `app` directory to tell search engine crawlers which URLs they can access on your site.

## Static `robots.txt` {#static-robotstxt}


app/robots.txt
```
User-Agent: *
Allow: /
Disallow: /private/

Sitemap: https://acme.com/sitemap.xml
```

## Generate a Robots file {#generate-a-robots-file}

Add a `robots.js` or `robots.ts` file that returns a [`Robots` object](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/robots#robots-object).


app/robots.ts
```
import { MetadataRoute } from 'next'
 
export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: '/private/',
    },
    sitemap: 'https://acme.com/sitemap.xml',
  }
}
```

Output:

```
User-Agent: *
Allow: /
Disallow: /private/

Sitemap: https://acme.com/sitemap.xml
```

### Robots object {#robots-object}

```
type Robots = {
  rules:
    | {
        userAgent?: string | string[]
        allow?: string | string[]
        disallow?: string | string[]
        crawlDelay?: number
      }
    | Array<{
        userAgent: string | string[]
        allow?: string | string[]
        disallow?: string | string[]
        crawlDelay?: number
      }>
  sitemap?: string | string[]
  host?: string
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|`robots` introduced.|

