---
linkTitle: "Defining Routes"
title: "Routing: Defining Routes | Next.js"
description: "Learn how to create your first route in Next.js."
weight: 1
type: docs
---

# Defining Routes

> We recommend reading the [Routing Fundamentals ↗](https://nextjs.org/docs/app/building-your-application/routing.html) page before continuing.
> 

This page will guide you through how to define and organize routes in your Next.js application.

## Creating Routes {#creating-routes}

Next.js uses a file-system based router where **folders** are used to define routes.

Each folder represents a [**route** segment ↗](https://nextjs.org/docs/app/building-your-application/routing.html#route-segments) that maps to a **URL** segment. To create a [nested route ↗](https://nextjs.org/docs/app/building-your-application/routing.html#nested-routes), you can nest folders inside each other.

![Route segments to path segments](/assets/nextjs/13.5/docs/light/route-segments-to-path-segments.png)
![Route segments to path segments](/assets/nextjs/13.5/docs/dark/route-segments-to-path-segments.png)

A special [`page.js` file](/docs/app/building-your-application/routing/pages-and-layouts.html#pages) is used to make route segments publicly accessible.

![Defining Routes](/assets/nextjs/13.5/docs/light/defining-routes.png)
![Defining Routes](/assets/nextjs/13.5/docs/dark/defining-routes.png)

In this example, the `/dashboard/analytics` URL path is *not* publicly accessible because it does not have a corresponding `page.js` file. This folder could be used to store components, stylesheets, images, or other colocated files.

> **Good to know**: `.js`, `.jsx`, or `.tsx` file extensions can be used for special files.
> 

## Creating UI {#creating-ui}

[Special file conventions](/docs/app/building-your-application/routing.html#file-conventions) are used to create UI for each route segment. The most common are [pages](/docs/app/building-your-application/routing/pages-and-layouts.html#pages) to show UI unique to a route, and [layouts](/docs/app/building-your-application/routing/pages-and-layouts.html#layouts) to show UI that is shared across multiple routes.

For example, to create your first page, add a `page.js` file inside the `app` directory and export a React component:


app/page.tsx
```
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```
