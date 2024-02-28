---
linkTitle: "sitemap.xml"
title: "Metadata Files: sitemap.xml | Next.js"
description: "API Reference for the sitemap.xml file."
weight: 5
type: docs
---

# sitemap.xml

Add or generate a `sitemap.xml` file that matches the [Sitemaps XML format ↗](https://www.sitemaps.org/protocol.html) in the **root** of `app` directory to help search engine crawlers crawl your site more efficiently.

## Static `sitemap.xml` {#static-sitemapxml}


app/sitemap.xml
```
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://acme.com</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>yearly</changefreq>
    <priority>1</priority>
  </url>
  <url>
    <loc>https://acme.com/about</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://acme.com/blog</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.5</priority>
  </url>
</urlset>
```

## Generate a Sitemap {#generate-a-sitemap}

Add a `sitemap.js` or `sitemap.ts` file that returns [`Sitemap`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/sitemap#sitemap-return-type).


app/sitemap.ts
```
import { MetadataRoute } from 'next'
 
export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: 'https://acme.com',
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 1,
    },
    {
      url: 'https://acme.com/about',
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: 'https://acme.com/blog',
      lastModified: new Date(),
      changeFrequency: 'weekly',
      priority: 0.5,
    },
  ]
}
```

Output:


acme.com/sitemap.xml
```
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://acme.com</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>yearly</changefreq>
    <priority>1</priority>
  </url>
  <url>
    <loc>https://acme.com/about</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://acme.com/blog</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.5</priority>
  </url>
</urlset>
```

### Sitemap Return Type {#sitemap-return-type}

```
type Sitemap = Array<{
  url: string
  lastModified?: string | Date
  changeFrequency?:
    | 'always'
    | 'hourly'
    | 'daily'
    | 'weekly'
    | 'monthly'
    | 'yearly'
    | 'never'
  priority?: number
}>
```

> **Good to know**
> - In the future, we will support multiple sitemaps and sitemap indexes.
> 

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.3.0`|`sitemap` introduced.|
|`v13.4.5`|Add `changeFrequency` and `priority` attributes to sitemaps.|

