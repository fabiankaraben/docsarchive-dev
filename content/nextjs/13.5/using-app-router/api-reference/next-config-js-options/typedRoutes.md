---
linkTitle: "typedRoutes"
title: "next.config.js Options: typedRoutes | Next.js"
description: "Enable experimental support for statically typed links."
weight: 30
type: docs
---

# typedRoutes (experimental)

Experimental support for [statically typed links](/nextjs/13.5/using-app-router/building-your-application/configuring/typescript#statically-typed-links). This feature requires using the App Router as well as TypeScript in your project.


next.config.js
```
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    typedRoutes: true,
  },
}
 
module.exports = nextConfig
```
