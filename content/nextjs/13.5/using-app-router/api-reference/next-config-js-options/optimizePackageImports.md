---
linkTitle: "optimizePackageImports"
title: "next.config.js Options: optimizePackageImports | Next.js"
description: ""
weight: 18
type: docs
---

# optimizePackageImports

Some packages can export hundreds or thousands of modules, which can cause performance issues in development and production.

Adding a package to `experimental.optimizePackageImports` will only load the modules you are actually using, while still giving you the convenience of writing import statements with many named exports.


next.config.js
```
module.exports = {
  experimental: {
    optimizePackageImports: ['package-name'],
  },
}
```

Libraries like `@mui/icons-material`, `@mui/material`, `date-fns`, `lodash`, `lodash-es`, `react-bootstrap`, `@headlessui/react`, `@heroicons/react`, and `lucide-react` are already optimized by default.
