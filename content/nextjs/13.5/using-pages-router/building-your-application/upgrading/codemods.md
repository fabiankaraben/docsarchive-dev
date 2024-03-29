---
linkTitle: "Codemods"
title: "Upgrading: Codemods | Next.js"
description: "Use codemods to upgrade your Next.js codebase when new features are released."
weight: 1
type: docs
---

# Codemods

Codemods are transformations that run on your codebase programmatically. This allows a large number of changes to be programmatically applied without having to manually go through every file.

Next.js provides Codemod transformations to help upgrade your Next.js codebase when an API is updated or deprecated.

## Usage {#usage}

In your terminal, navigate (`cd`) into your project's folder, then run:


Terminal
```
npx @next/codemod <transform> <path>
```

Replacing `<transform>` and `<path>` with appropriate values.

- `transform` - name of transform
- `path` - files or directory to transform
- `--dry` Do a dry-run, no code will be edited
- `--print` Prints the changed output for comparison

## Next.js Codemods {#nextjs-codemods}

### 14.0 {#140}

#### Migrate `ImageResponse` imports {#migrate-imageresponse-imports}

##### `next-og-import` {#next-og-import}


Terminal
```
npx @next/codemod@latest next-og-import .
```

This codemod moves transforms imports from `next/server` to `next/og` for usage of [Dynamic OG Image Generation](/nextjs/13.5/using-app-router/building-your-application/optimizing/metadata#dynamic-image-generation).

For example:

```
import { ImageResponse } from 'next/server'
```

Transforms into:

```
import { ImageResponse } from 'next/og'
```

#### Use `viewport` export {#use-viewport-export}

##### `metadata-to-viewport-export` {#metadata-to-viewport-export}


Terminal
```
npx @next/codemod@latest metadata-to-viewport-export .
```

This codemod migrates certain viewport metadata to `viewport` export.

For example:

```
export const metadata = {
  title: 'My App',
  themeColor: 'dark',
  viewport: {
    width: 1,
  },
}
```

Transforms into:

```
export const metadata = {
  title: 'My App',
}
 
export const viewport = {
  width: 1,
  themeColor: 'dark',
}
```

### 13.2 {#132}

#### Use Built-in Font {#use-built-in-font}

##### `built-in-next-font` {#built-in-next-font}


Terminal
```
npx @next/codemod@latest built-in-next-font .
```

This codemod uninstalls the `@next/font` package and transforms `@next/font` imports into the built-in `next/font`.

For example:

```
import { Inter } from '@next/font/google'
```

Transforms into:

```
import { Inter } from 'next/font/google'
```

### 13.0 {#130}

#### Rename Next Image Imports {#rename-next-image-imports}

##### `next-image-to-legacy-image` {#next-image-to-legacy-image}


Terminal
```
npx @next/codemod@latest next-image-to-legacy-image .
```

Safely renames `next/image` imports in existing Next.js 10, 11, or 12 applications to `next/legacy/image` in Next.js 13. Also renames `next/future/image` to `next/image`.

For example:


pages/index.js
```
import Image1 from 'next/image'
import Image2 from 'next/future/image'
 
export default function Home() {
  return (
    <div>
      <Image1 src="/test.jpg" width="200" height="300" />
      <Image2 src="/test.png" width="500" height="400" />
    </div>
  )
}
```

Transforms into:


pages/index.js
```
// 'next/image' becomes 'next/legacy/image'
import Image1 from 'next/legacy/image'
// 'next/future/image' becomes 'next/image'
import Image2 from 'next/image'
 
export default function Home() {
  return (
    <div>
      <Image1 src="/test.jpg" width="200" height="300" />
      <Image2 src="/test.png" width="500" height="400" />
    </div>
  )
}
```

#### Migrate to the New Image Component {#migrate-to-the-new-image-component}

##### `next-image-experimental` {#next-image-experimental}


Terminal
```
npx @next/codemod@latest next-image-experimental .
```

Dangerously migrates from `next/legacy/image` to the new `next/image` by adding inline styles and removing unused props.

- Removes `layout` prop and adds `style`.
- Removes `objectFit` prop and adds `style`.
- Removes `objectPosition` prop and adds `style`.
- Removes `lazyBoundary` prop.
- Removes `lazyRoot` prop.

#### Remove `<a>` Tags From Link Components {#remove-a-tags-from-link-components}

##### `new-link` {#new-link}


Terminal
```
npx @next/codemod@latest new-link .
```

Remove `<a>` tags inside [Link Components](/nextjs/13.5/using-pages-router/api-reference/components/link), or add a `legacyBehavior` prop to Links that cannot be auto-fixed.

For example:

```
<Link href="/about">
  <a>About</a>
</Link>
// transforms into
<Link href="/about">
  About
</Link>
 
<Link href="/about">
  <a onClick={() => console.log('clicked')}>About</a>
</Link>
// transforms into
<Link href="/about" onClick={() => console.log('clicked')}>
  About
</Link>
```

In cases where auto-fixing can't be applied, the `legacyBehavior` prop is added. This allows your app to keep functioning using the old behavior for that particular link.

```
const Component = () => <a>About</a>
 
<Link href="/about">
  <Component />
</Link>
// becomes
<Link href="/about" legacyBehavior>
  <Component />
</Link>
```

### 11 {#11}

#### Migrate from CRA {#migrate-from-cra}

##### `cra-to-next` {#cra-to-next}


Terminal
```
npx @next/codemod cra-to-next
```

Migrates a Create React App project to Next.js; creating a Pages Router and necessary config to match behavior. Client-side only rendering is leveraged initially to prevent breaking compatibility due to `window` usage during SSR and can be enabled seamlessly to allow the gradual adoption of Next.js specific features.

Please share any feedback related to this transform [in this discussion ↗](https://github.com/vercel/next.js/discussions/25858).

### 10 {#10}

#### Add React imports {#add-react-imports}

##### `add-missing-react-import` {#add-missing-react-import}


Terminal
```
npx @next/codemod add-missing-react-import
```

Transforms files that do not import `React` to include the import in order for the new [React JSX transform ↗](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html) to work.

For example:


my-component.js
```
export default class Home extends React.Component {
  render() {
    return <div>Hello World</div>
  }
}
```

Transforms into:


my-component.js
```
import React from 'react'
export default class Home extends React.Component {
  render() {
    return <div>Hello World</div>
  }
}
```

### 9 {#9}

#### Transform Anonymous Components into Named Components {#transform-anonymous-components-into-named-components}

##### `name-default-component` {#name-default-component}


Terminal
```
npx @next/codemod name-default-component
```

**Versions 9 and above.**

Transforms anonymous components into named components to make sure they work with [Fast Refresh ↗](https://nextjs.org/blog/next-9-4#fast-refresh).

For example:


my-component.js
```
export default function () {
  return <div>Hello World</div>
}
```

Transforms into:


my-component.js
```
export default function MyComponent() {
  return <div>Hello World</div>
}
```

The component will have a camel-cased name based on the name of the file, and it also works with arrow functions.

### 8 {#8}

#### Transform AMP HOC into page config {#transform-amp-hoc-into-page-config}

##### `withamp-to-config` {#withamp-to-config}


Terminal
```
npx @next/codemod withamp-to-config
```

Transforms the `withAmp` HOC into Next.js 9 page configuration.

For example:

```
// Before
import { withAmp } from 'next/amp'
 
function Home() {
  return <h1>My AMP Page</h1>
}
 
export default withAmp(Home)
```

```
// After
export default function Home() {
  return <h1>My AMP Page</h1>
}
 
export const config = {
  amp: true,
}
```

### 6 {#6}

#### Use `withRouter` {#use-withrouter}

##### `url-to-withrouter` {#url-to-withrouter}


Terminal
```
npx @next/codemod url-to-withrouter
```

Transforms the deprecated automatically injected `url` property on top level pages to using `withRouter` and the `router` property it injects. Read more here: [https://nextjs.org/docs/messages/url-deprecated ↗](https://nextjs.org/docs/messages/url-deprecated)

For example:


From
```
import React from 'react'
export default class extends React.Component {
  render() {
    const { pathname } = this.props.url
    return <div>Current pathname: {pathname}</div>
  }
}
```


To
```
import React from 'react'
import { withRouter } from 'next/router'
export default withRouter(
  class extends React.Component {
    render() {
      const { pathname } = this.props.router
      return <div>Current pathname: {pathname}</div>
    }
  }
)
```

This is one case. All the cases that are transformed (and tested) can be found in the [`__testfixtures__` directory ↗](https://github.com/vercel/next.js/tree/canary/packages/next-codemod/transforms/__testfixtures__/url-to-withrouter).
