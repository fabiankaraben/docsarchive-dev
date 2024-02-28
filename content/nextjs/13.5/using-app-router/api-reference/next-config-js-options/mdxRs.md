---
linkTitle: "mdxRs"
title: "next.config.js Options: mdxRs | Next.js"
description: "Use the new Rust compiler to compile MDX files in the App Router."
weight: 16
type: docs
---

# mdxRs

For use with `@next/mdx`. Compile MDX files using the new Rust compiler.


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
