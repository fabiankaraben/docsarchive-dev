---
linkTitle: "transpilePackages"
title: "next.config.js Options: transpilePackages | Next.js"
description: "Automatically transpile and bundle dependencies from local packages (like monorepos) or from external dependencies (`node_modules`)."
weight: 24
type: docs
---

# transpilePackages

Next.js can automatically transpile and bundle dependencies from local packages (like monorepos) or from external dependencies (`node_modules`). This replaces the `next-transpile-modules` package.


next.config.js
```
/** @type {import('next').NextConfig} */
const nextConfig = {
  transpilePackages: ['@acme/ui', 'lodash-es'],
}
 
module.exports = nextConfig
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.0.0`|`transpilePackages` added.|

