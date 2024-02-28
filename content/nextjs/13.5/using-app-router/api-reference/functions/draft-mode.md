---
linkTitle: "draftMode"
title: "Functions: draftMode | Next.js"
description: "API Reference for the draftMode function."
weight: 2
type: docs
---

# draftMode

The `draftMode` function allows you to detect [Draft Mode](/nextjs/13.5/using-app-router/building-your-application/configuring/draft-mode) inside a [Server Component ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html).


app/page.js
```
import { draftMode } from 'next/headers'
 
export default function Page() {
  const { isEnabled } = draftMode()
  return (
    <main>
      <h1>My Blog Post</h1>
      <p>Draft Mode is currently {isEnabled ? 'Enabled' : 'Disabled'}</p>
    </main>
  )
}
```

## Version History {#version-history}

|Version|Changes|
|---|---|
|`v13.4.0`|`draftMode` introduced.|

