---
linkTitle: "distDir"
title: "next.config.js Options: distDir | Next.js"
description: "Set a custom build directory to use instead of the default .next directory."
weight: 5
type: docs
---

# distDir

You can specify a name to use for a custom build directory to use instead of `.next`.

Open `next.config.js` and add the `distDir` config:


next.config.js
```
module.exports = {
  distDir: 'build',
}
```

Now if you run `next build` Next.js will use `build` instead of the default `.next` folder.

> `distDir`**should not** leave your project directory. For example, `../build` is an **invalid** directory.
> 
