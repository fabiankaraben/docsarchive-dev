---
linkTitle: "Absolute Imports and Module Path Aliases"
title: "Configuring: Absolute Imports and Module Path Aliases | Next.js"
description: "Configure module path aliases that allow you to remap certain import paths."
weight: 4
type: docs
---

# Absolute Imports and Module Path Aliases

Next.js has in-built support for the `"paths"` and `"baseUrl"` options of `tsconfig.json` and `jsconfig.json` files.

These options allow you to alias project directories to absolute paths, making it easier to import modules. For example:

```
// before
import { Button } from '../../../components/button'
 
// after
import { Button } from '@/components/button'
```

> **Good to know**: `create-next-app` will prompt to configure these options for you.
> 

## Absolute Imports {#absolute-imports}

The `baseUrl` configuration option allows you to import directly from the root of the project.

An example of this configuration:


tsconfig.json or jsconfig.json
```
{
  "compilerOptions": {
    "baseUrl": "."
  }
}
```


components/button.tsx
```
export default function Button() {
  return <button>Click me</button>
}
```


app/page.tsx
```
import Button from 'components/button'
 
export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```

## Module Aliases {#module-aliases}

In addition to configuring the `baseUrl` path, you can use the `"paths"` option to "alias" module paths.

For example, the following configuration maps `@/components/*` to `components/*`:


tsconfig.json or jsconfig.json
```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"]
    }
  }
}
```


components/button.tsx
```
export default function Button() {
  return <button>Click me</button>
}
```


app/page.tsx
```
import Button from '@/components/button'
 
export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```

Each of the `"paths"` are relative to the `baseUrl` location. For example:

```
// tsconfig.json or jsconfig.json
{
  "compilerOptions": {
    "baseUrl": "src/",
    "paths": {
      "@/styles/*": ["styles/*"],
      "@/components/*": ["components/*"]
    }
  }
}
```

```
// pages/index.js
import Button from '@/components/button'
import '@/styles/styles.css'
import Helper from 'utils/helper'
 
export default function HomePage() {
  return (
    <Helper>
      <h1>Hello World</h1>
      <Button />
    </Helper>
  )
}
```
