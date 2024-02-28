---
linkTitle: "Pages and Layouts"
title: "Routing: Pages and Layouts | Next.js"
description: "Create your first page and shared layout with the App Router."
weight: 2
type: docs
---

# Pages and Layouts

> We recommend reading the [Routing Fundamentals ↗](https://nextjs.org/docs/app/building-your-application/routing.html) and [Defining Routes](/nextjs/13.5/using-app-router/building-your-application/routing/defining-routes) pages before continuing.
> 

The App Router inside Next.js 13 introduced new file conventions to easily create [pages](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts#pages), [shared layouts](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts#layouts), and [templates](/nextjs/13.5/using-app-router/building-your-application/routing/pages-and-layouts#templates). This page will guide you through how to use these special files in your Next.js application.

## Pages {#pages}

A page is UI that is **unique** to a route. You can define pages by exporting a component from a `page.js` file. Use nested folders to [define a route](/nextjs/13.5/using-app-router/building-your-application/routing/defining-routes) and a `page.js` file to make the route publicly accessible.

Create your first page by adding a `page.js` file inside the `app` directory:

![page.js special file](/assets/nextjs/13.5/docs/light/page-special-file.png)
![page.js special file](/assets/nextjs/13.5/docs/dark/page-special-file.png)


app/page.tsx
```
// `app/page.tsx` is the UI for the `/` URL
export default function Page() {
  return <h1>Hello, Home page!</h1>
}
```


app/dashboard/page.tsx
```
// `app/dashboard/page.tsx` is the UI for the `/dashboard` URL
export default function Page() {
  return <h1>Hello, Dashboard Page!</h1>
}
```

> **Good to know**:
> - A page is always the [leaf](/docs/app/building-your-application/routing.html#terminology) of the [route subtree](/docs/app/building-your-application/routing.html#terminology).
> - `.js`, `.jsx`, or `.tsx` file extensions can be used for Pages.
> - A `page.js` file is required to make a route segment publicly accessible.
> - Pages are [Server Components](/docs/app/building-your-application/rendering/server-components.html) by default but can be set to a [Client Component](/docs/app/building-your-application/rendering/client-components.html).
> - Pages can fetch data. View the [Data Fetching](/docs/app/building-your-application/data-fetching.html) section for more information.
> 

## Layouts {#layouts}

A layout is UI that is **shared** between multiple pages. On navigation, layouts preserve state, remain interactive, and do not re-render. Layouts can also be [nested](/docs/app/building-your-application/routing/pages-and-layouts.html#nesting-layouts).

You can define a layout by `default` exporting a React component from a `layout.js` file. The component should accept a `children` prop that will be populated with a child layout (if it exists) or a child page during rendering.

![layout.js special file](/assets/nextjs/13.5/docs/light/layout-special-file.png)
![layout.js special file](/assets/nextjs/13.5/docs/dark/layout-special-file.png)


app/dashboard/layout.tsx
```
export default function DashboardLayout({
  children, // will be a page or nested layout
}: {
  children: React.ReactNode
}) {
  return (
    <section>
      {/* Include shared UI here e.g. a header or sidebar */}
      <nav></nav>
 
      {children}
    </section>
  )
}
```

> **Good to know**:
> - The top-most layout is called the [Root Layout](/docs/app/building-your-application/routing/pages-and-layouts.html#root-layout-required). This **required** layout is shared across all pages in an application. Root layouts must contain `html` and `body` tags.
> - Any route segment can optionally define its own [Layout](/docs/app/building-your-application/routing/pages-and-layouts.html#nesting-layouts). These layouts will be shared across all pages in that segment.
> - Layouts in a route are **nested** by default. Each parent layout wraps child layouts below it using the React `children` prop.
> - You can use [Route Groups](/docs/app/building-your-application/routing/route-groups.html) to opt specific route segments in and out of shared layouts.
> - Layouts are [Server Components](/docs/app/building-your-application/rendering/server-components.html) by default but can be set to a [Client Component](/docs/app/building-your-application/rendering/client-components.html).
> - Layouts can fetch data. View the [Data Fetching](/docs/app/building-your-application/data-fetching.html) section for more information.
> - Passing data between a parent layout and its children is not possible. However, you can fetch the same data in a route more than once, and React will [automatically dedupe the requests](/docs/app/building-your-application/caching.html#request-memoization) without affecting performance.
> - Layouts do not have access to the route segments below itself. To access all route segments, you can use [`useSelectedLayoutSegment`](/docs/app/api-reference/functions/use-selected-layout-segment.html) or [`useSelectedLayoutSegments`](/docs/app/api-reference/functions/use-selected-layout-segments.html) in a Client Component.
> - `.js`, `.jsx`, or `.tsx` file extensions can be used for Layouts.
> - A `layout.js` and `page.js` file can be defined in the same folder. The layout will wrap the page.
> 

### Root Layout (Required) {#root-layout-required}

The root layout is defined at the top level of the `app` directory and applies to all routes. This layout enables you to modify the initial HTML returned from the server.


app/layout.tsx
```
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

> **Good to know**:
> - The `app` directory **must** include a root layout.
> - The root layout must define `<html>` and `<body>` tags since Next.js does not automatically create them.
> - You can use the [built-in SEO support](/docs/app/building-your-application/optimizing/metadata.html) to manage `<head>` HTML elements, for example, the `<title>` element.
> - You can use [route groups](/docs/app/building-your-application/routing/route-groups.html) to create multiple root layouts. See an [example here](/docs/app/building-your-application/routing/route-groups.html#creating-multiple-root-layouts).
> - The root layout is a [Server Component](/docs/app/building-your-application/rendering/server-components.html) by default and **can not** be set to a [Client Component](/docs/app/building-your-application/rendering/client-components.html).
> 

> **Migrating from the `pages` directory:** The root layout replaces the [`_app.js`](/docs/pages/building-your-application/routing/custom-app.html) and [`_document.js`](/docs/pages/building-your-application/routing/custom-document.html) files. [View the migration guide](/docs/app/building-your-application/upgrading/app-router-migration.html#migrating-_documentjs-and-_appjs).
> 

### Nesting Layouts {#nesting-layouts}

Layouts defined inside a folder (e.g. `app/dashboard/layout.js`) apply to specific route segments (e.g. `acme.com/dashboard`) and render when those segments are active. By default, layouts in the file hierarchy are **nested**, which means they wrap child layouts via their `children` prop.

![Nested Layout](/assets/nextjs/13.5/docs/light/nested-layout.png)
![Nested Layout](/assets/nextjs/13.5/docs/dark/nested-layout.png)


app/dashboard/layout.tsx
```
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

> **Good to know**:
> - Only the root layout can contain `<html>` and `<body>` tags.
> 

If you were to combine the two layouts above, the root layout (`app/layout.js`) would wrap the dashboard layout (`app/dashboard/layout.js`), which would wrap route segments inside `app/dashboard/*`.

The two layouts would be nested as such:

![Nested Layouts](/assets/nextjs/13.5/docs/light/nested-layouts-ui.png)
![Nested Layouts](/assets/nextjs/13.5/docs/dark/nested-layouts-ui.png)

You can use [Route Groups](/docs/app/building-your-application/routing/route-groups.html) to opt specific route segments in and out of shared layouts.

## Templates {#templates}

Templates are similar to layouts in that they wrap each child layout or page. Unlike layouts that persist across routes and maintain state, templates create a new instance for each of their children on navigation. This means that when a user navigates between routes that share a template, a new instance of the component is mounted, DOM elements are recreated, state is **not** preserved, and effects are re-synchronized.

There may be cases where you need those specific behaviors, and templates would be a more suitable option than layouts. For example:

- Features that rely on `useEffect` (e.g logging page views) and `useState` (e.g a per-page feedback form).
- To change the default framework behavior. For example, Suspense Boundaries inside layouts only show the fallback the first time the Layout is loaded and not when switching pages. For templates, the fallback is shown on each navigation.

A template can be defined by exporting a default React component from a `template.js` file. The component should accept a `children` prop.

![template.js special file](/assets/nextjs/13.5/docs/light/template-special-file.png)
![template.js special file](/assets/nextjs/13.5/docs/dark/template-special-file.png)


app/template.tsx
```
export default function Template({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>
}
```

In terms of nesting, `template.js` is rendered between a layout and its children. Here's a simplified output:


Output
```
<Layout>
  {/* Note that the template is given a unique key. */}
  <Template key={routeParam}>{children}</Template>
</Layout>
```

## Modifying `<head>` {#modifying-head}

In the `app` directory, you can modify the `<head>` HTML elements such as `title` and `meta` using the [built-in SEO support](/docs/app/building-your-application/optimizing/metadata.html).

Metadata can be defined by exporting a [`metadata` object](/docs/app/api-reference/functions/generate-metadata.html#the-metadata-object) or [`generateMetadata` function](/docs/app/api-reference/functions/generate-metadata.html#generatemetadata-function) in a [`layout.js`](/docs/app/api-reference/file-conventions/layout.html) or [`page.js`](/docs/app/api-reference/file-conventions/page.html) file.


app/page.tsx
```
import { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'Next.js',
}
 
export default function Page() {
  return '...'
}
```

> **Good to know**: You should **not** manually add `<head>` tags such as `<title>` and `<meta>` to root layouts. Instead, you should use the [Metadata API](/docs/app/api-reference/functions/generate-metadata.html) which automatically handles advanced requirements such as streaming and de-duplicating `<head>` elements.
> 

[Learn more about available metadata options in the API reference.](/docs/app/api-reference/functions/generate-metadata.html)
