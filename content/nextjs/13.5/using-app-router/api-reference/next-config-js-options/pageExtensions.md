---
linkTitle: "pageExtensions"
title: "next.config.js Options: pageExtensions | Next.js"
description: "Extend the default page extensions used by Next.js when resolving pages in the Pages Router."
weight: 20
type: docs
---

# pageExtensions

By default, Next.js accepts files with the following extensions: `.tsx`, `.ts`, `.jsx`, `.js`. This can be modified to allow other extensions like markdown (`.md`, `.mdx`).


next.config.js
```
const withMDX = require('@next/mdx')()
 
/** @type {import('next').NextConfig} */
const nextConfig = {
  pageExtensions: ['ts', 'tsx', 'mdx'],
  experimental: {
    mdxRs: true,
  },
}
 
module.exports = withMDX(nextConfig)
```
