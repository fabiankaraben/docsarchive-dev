---
linkTitle: "poweredByHeader"
title: "next.config.js Options: poweredByHeader | Next.js"
description: "Next.js will add the `x-powered-by` header by default. Learn to opt-out of it here."
weight: 17
type: docs
---

# poweredByHeader

By default Next.js will add the `x-powered-by` header. To opt-out of it, open `next.config.js` and disable the `poweredByHeader` config:


next.config.js
```
module.exports = {
  poweredByHeader: false,
}
```
