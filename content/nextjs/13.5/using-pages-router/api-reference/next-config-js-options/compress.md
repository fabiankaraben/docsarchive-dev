---
linkTitle: "compress"
title: "next.config.js Options: compress | Next.js"
description: "Next.js provides gzip compression to compress rendered content and static files, it only works with the server target. Learn more about it here."
weight: 3
type: docs
---

# compress

Next.js provides [**gzip** ↗](https://tools.ietf.org/html/rfc6713#section-3) compression to compress rendered content and static files. In general you will want to enable compression on a HTTP proxy like [nginx ↗](https://www.nginx.com/), to offload load from the `Node.js` process.

To disable **compression**, open `next.config.js` and disable the `compress` config:


next.config.js
```
module.exports = {
  compress: false,
}
```
