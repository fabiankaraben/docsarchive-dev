---
linkTitle: "MDX"
title: "Configuring: MDX | Next.js"
description: "Learn how to configure MDX to write JSX in your markdown files."
weight: 5
type: docs
---

# MDX

[Markdown ↗](https://daringfireball.net/projects/markdown/syntax) is a lightweight markup language used to format text. It allows you to write using plain text syntax and convert it to structurally valid HTML. It's commonly used for writing content on websites and blogs.

You write...

```
I **love** using [Next.js ↗](https://nextjs.org/)
```

Output:

```
<p>I <strong>love</strong> using <a href="https://nextjs.org/">Next.js</a></p>
```

[MDX ↗](https://mdxjs.com/) is a superset of markdown that lets you write [JSX ↗](https://react.dev/learn/writing-markup-with-jsx) directly in your markdown files. It is a powerful way to add dynamic interactivity and embed React components within your content.

Next.js can support both local MDX content inside your application, as well as remote MDX files fetched dynamically on the server. The Next.js plugin handles transforming Markdown and React components into HTML, including support for usage in Server Components (default in `app`).

## `@next/mdx` {#nextmdx}

The `@next/mdx` package is configured in the `next.config.js` file at your projects root. **It sources data from local files**, allowing you to create pages with a `.mdx` extension, directly in your `/pages` or `/app` directory.

## Getting Started {#getting-started}

Install the `@next/mdx` package:


Terminal
```
npm install @next/mdx @mdx-js/loader @mdx-js/react @types/mdx
```

Create `mdx-components.tsx` in the root of your application (the parent folder of `app/` or `src/`):


mdx-components.tsx
```
import type { MDXComponents } from 'mdx/types'
 
// This file allows you to provide custom React components
// to be used in MDX files. You can import and use any
// React component you want, including components from
// other libraries.
 
// This file is required to use MDX in `app` directory.
export function useMDXComponents(components: MDXComponents): MDXComponents {
  return {
    // Allows customizing built-in components, e.g. to add styling.
    // h1: ({ children }) => <h1 style={{ fontSize: "100px" }}>{children}</h1>,
    ...components,
  }
}
```

Update `next.config.js` to use `mdxRs`:


next.config.js
```
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    mdxRs: true,
  },
}
 
const withMDX = require('@next/mdx')()
module.exports = withMDX(nextConfig)
```

Add a new file with MDX content to your `app` directory:


app/hello.mdx
```
Hello, Next.js!
 
You can import and use React components in MDX files.
```

Import the MDX file inside a `page` to display the content:


app/page.tsx
```
import HelloWorld from './hello.mdx'
 
export default function Page() {
  return <HelloWorld />
}
```

## Remote MDX {#remote-mdx}

If your Markdown or MDX files do *not* live inside your application, you can fetch them dynamically on the server. This is useful for fetching content from a CMS or other data source.

There are two popular community packages for fetching MDX content: [`next-mdx-remote` ↗](https://github.com/hashicorp/next-mdx-remote#react-server-components-rsc--nextjs-app-directory-support) and [`contentlayer` ↗](https://www.contentlayer.dev/). For example, the following example uses `next-mdx-remote`:

> **Good to know**: Please proceed with caution. MDX compiles to JavaScript and is executed on the server. You should only fetch MDX content from a trusted source, otherwise this can lead to remote code execution (RCE).
> 


app/page.tsx
```
import { MDXRemote } from 'next-mdx-remote/rsc'
 
export default async function Home() {
  const res = await fetch('https://...')
  const markdown = await res.text()
  return <MDXRemote source={markdown} />
}
```

## Layouts {#layouts}

To share a layout around MDX content, you can use the [built-in layouts support](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts#layouts) with the App Router.

## Remark and Rehype Plugins {#remark-and-rehype-plugins}

You can optionally provide `remark` and `rehype` plugins to transform the MDX content. For example, you can use `remark-gfm` to support GitHub Flavored Markdown.

Since the `remark` and `rehype` ecosystem is ESM only, you'll need to use `next.config.mjs` as the configuration file.


next.config.mjs
```
import remarkGfm from 'remark-gfm'
import createMDX from '@next/mdx'
 
/** @type {import('next').NextConfig} */
const nextConfig = {}
 
const withMDX = createMDX({
  options: {
    extension: /\.mdx?$/,
    remarkPlugins: [remarkGfm],
    rehypePlugins: [],
    // If you use `MDXProvider`, uncomment the following line.
    // providerImportSource: "@mdx-js/react",
  },
})
export default withMDX(nextConfig)
```

## Frontmatter {#frontmatter}

Frontmatter is a YAML like key/value pairing that can be used to store data about a page. `@next/mdx` does **not** support frontmatter by default, though there are many solutions for adding frontmatter to your MDX content, such as [gray-matter ↗](https://github.com/jonschlinkert/gray-matter).

To access page metadata with `@next/mdx`, you can export a meta object from within the `.mdx` file:

```
export const meta = {
  author: 'Rich Haines',
}
 
# My MDX page
```

## Custom Elements {#custom-elements}

One of the pleasant aspects of using markdown, is that it maps to native `HTML` elements, making writing fast, and intuitive:

```
This is a list in markdown:
 
- One
- Two
- Three
```

The above generates the following `HTML`:

```
<p>This is a list in markdown:</p>
 
<ul>
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

When you want to style your own elements to give a custom feel to your website or application, you can pass in shortcodes. These are your own custom components that map to `HTML` elements. To do this you use the `MDXProvider` and pass a components object as a prop. Each object key in the components object maps to a `HTML` element name.

To enable you need to specify `providerImportSource: "@mdx-js/react"` in `next.config.js`.


next.config.js
```
const withMDX = require('@next/mdx')({
  // ...
  options: {
    providerImportSource: '@mdx-js/react',
  },
})
```

Then setup the provider in your page


pages/index.js
```
import { MDXProvider } from '@mdx-js/react'
import Image from 'next/image'
import { Heading, InlineCode, Pre, Table, Text } from 'my-components'
 
const ResponsiveImage = (props) => (
  <Image
    alt={props.alt}
    sizes="100vw"
    style={{ width: '100%', height: 'auto' }}
    {...props}
  />
)
 
const components = {
  img: ResponsiveImage,
  h1: Heading.H1,
  h2: Heading.H2,
  p: Text,
  pre: Pre,
  code: InlineCode,
}
 
export default function Post(props) {
  return (
    <MDXProvider components={components}>
      <main {...props} />
    </MDXProvider>
  )
}
```

If you use it across the site you may want to add the provider to `_app.js` so all MDX pages pick up the custom element config.

## Deep Dive: How do you transform markdown into HTML? {#deep-dive-how-do-you-transform-markdown-into-html}

React does not natively understand Markdown. The markdown plaintext needs to first be transformed into HTML. This can be accomplished with `remark` and `rehype`.

`remark` is an ecosystem of tools around markdown. `rehype` is the same, but for HTML. For example, the following code snippet transforms markdown into HTML:

```
import { unified } from 'unified'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import rehypeSanitize from 'rehype-sanitize'
import rehypeStringify from 'rehype-stringify'
 
main()
 
async function main() {
  const file = await unified()
    .use(remarkParse) // Convert into markdown AST
    .use(remarkRehype) // Transform to HTML AST
    .use(rehypeSanitize) // Sanitize HTML input
    .use(rehypeStringify) // Convert AST into serialized HTML
    .process('Hello, Next.js!')
 
  console.log(String(file)) // <p>Hello, Next.js!</p>
}
```

The `remark` and `rehype` ecosystem contains plugins for [syntax highlighting ↗](https://github.com/atomiks/rehype-pretty-code), [linking headings ↗](https://github.com/rehypejs/rehype-autolink-headings), [generating a table of contents ↗](https://github.com/remarkjs/remark-toc), and more.

When using `@next/mdx` as shown below, you **do not** need to use `remark` or `rehype` directly, as it is handled for you.

## Using the Rust-based MDX compiler (Experimental) {#using-the-rust-based-mdx-compiler-experimental}

Next.js supports a new MDX compiler written in Rust. This compiler is still experimental and is not recommended for production use. To use the new compiler, you need to configure `next.config.js` when you pass it to `withMDX`:


next.config.js
```
module.exports = withMDX({
  experimental: {
    mdxRs: true,
  },
})
```

## Helpful Links {#helpful-links}

- [MDX ↗](https://mdxjs.com/)
- [`@next/mdx` ↗](https://www.npmjs.com/package/@next/mdx)
- [remark ↗](https://github.com/remarkjs/remark)
- [rehype ↗](https://github.com/rehypejs/rehype)
