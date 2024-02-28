---
linkTitle: "Caching"
title: "Building Your Application: Caching | Next.js"
description: "An overview of caching mechanisms in Next.js."
weight: 4
type: docs
prev: /nextjs/13.5/using-app-router/building-your-application/rendering/edge-and-nodejs-runtimes
---

# Caching in Next.js

Next.js improves your application's performance and reduces costs by caching rendering work and data requests. This page provides an in-depth look at Next.js caching mechanisms, the APIs you can use to configure them, and how they interact with each other.

> **Good to know**: This page helps you understand how Next.js works under the hood but is **not** essential knowledge to be productive with Next.js. Most of Next.js' caching heuristics are determined by your API usage and have defaults for the best performance with zero or minimal configuration.
> 

## Overview {#overview}

Here's a high-level overview of the different caching mechanisms and their purpose:

|Mechanism|What|Where|Purpose|Duration|
|---|---|---|---|---|
|[Request Memoization](/nextjs/13.5/using-app-router/building-your-application/caching#request-memoization)|Return values of functions|Server|Re-use data in a React Component tree|Per-request lifecycle|
|[Data Cache](/nextjs/13.5/using-app-router/building-your-application/caching#data-cache)|Data|Server|Store data across user requests and deployments|Persistent (can be revalidated)|
|[Full Route Cache](/nextjs/13.5/using-app-router/building-your-application/caching#full-route-cache)|HTML and RSC payload|Server|Reduce rendering cost and improve performance|Persistent (can be revalidated)|
|[Router Cache](/nextjs/13.5/using-app-router/building-your-application/caching#router-cache)|RSC Payload|Client|Reduce server requests on navigation|User session or time-based|


By default, Next.js will cache as much as possible to improve performance and reduce cost. This means routes are **statically rendered** and data requests are **cached** unless you opt out. The diagram below shows the default caching behavior: when a route is statically rendered at build time and when a static route is first visited.

![Diagram showing the default caching behavior in Next.js for the four mechanisms, with HIT, MISS and SET at build time and when a route is first visited.](/assets/nextjs/13.5/docs/light/caching-overview.png)
![Diagram showing the default caching behavior in Next.js for the four mechanisms, with HIT, MISS and SET at build time and when a route is first visited.](/assets/nextjs/13.5/docs/dark/caching-overview.png)

Caching behavior changes depending on whether the route is statically or dynamically rendered, data is cached or uncached, and whether a request is part of an initial visit or a subsequent navigation. Depending on your use case, you can configure the caching behavior for individual routes and data requests.

## Request Memoization {#request-memoization}

React extends the [`fetch` API](/docs/app/building-your-application/caching.html#fetch) to automatically **memoize** requests that have the same URL and options. This means you can call a fetch function for the same data in multiple places in a React component tree while only executing it once.

![Deduplicated Fetch Requests](/assets/nextjs/13.5/docs/light/deduplicated-fetch-requests.png)
![Deduplicated Fetch Requests](/assets/nextjs/13.5/docs/dark/deduplicated-fetch-requests.png)

For example, if you need to use the same data across a route (e.g. in a Layout, Page, and multiple components), you do not have to fetch data at the top of the tree then forward props between components. Instead, you can fetch data in the components that need it without worrying about the performance implications of making multiple requests across the network for the same data.


app/example.tsx
```
async function getItem() {
  // The `fetch` function is automatically memoized and the result
  // is cached
  const res = await fetch('https://.../item/1')
  return res.json()
}
 
// This function is called twice, but only executed the first time
const item = await getItem() // cache MISS
 
// The second call could be anywhere in your route
const item = await getItem() // cache HIT
```

**How Request Memoization Works**

![Diagram showing how fetch memoization works during React rendering.](/assets/nextjs/13.5/docs/light/request-memoization.png)
![Diagram showing how fetch memoization works during React rendering.](/assets/nextjs/13.5/docs/dark/request-memoization.png)

- While rendering a route, the first time a particular request is called, its result will not be in memory and it'll be a cache `MISS`.
- Therefore, the function will be executed, and the data will be fetched from the external source, and the result will be stored in memory.
- Subsequent function calls of the request in the same render pass will be a cache `HIT`, and the data will be returned from memory without executing the function.
- Once the route has been rendered and the rendering pass is complete, memory is "reset" and all request memoization entries are cleared.

> **Good to know**:
> - Request memoization is a React feature, not a Next.js feature. It's included here to show how it interacts with the other caching mechanisms.
> - Memoization only applies to the `GET` method in `fetch` requests.
> - Memoization only applies to the React Component tree, this means:- It applies to `fetch` requests in `generateMetadata`, `generateStaticParams`, Layouts, Pages, and other Server Components.
>   - It doesn't apply to `fetch` requests in Route Handlers as they are not a part of the React component tree.
> - For cases where `fetch` is not suitable (e.g. some database clients, CMS clients, or GraphQL clients), you can use the [React `cache` function](/docs/app/building-your-application/caching.html#react-cache-function) to memoize functions.
> 

### Duration {#duration}

The cache lasts the lifetime of a server request until the React component tree has finished rendering.

### Revalidating {#revalidating}

Since the memoization is not shared across server requests and only applies during rendering, there is no need to revalidate it.

### Opting out {#opting-out}

To opt out of memoization in `fetch` requests, you can pass an `AbortController``signal` to the request.


app/example.js
```
const { signal } = new AbortController()
fetch(url, { signal })
```

## Data Cache {#data-cache}

Next.js has a built-in Data Cache that **persists** the result of data fetches across incoming **server requests** and **deployments**. This is possible because Next.js extends the native `fetch` API to allow each request on the server to set its own persistent caching semantics.

> **Good to know**: In the browser, the `cache` option of `fetch` indicates how a request will interact with the browser's HTTP cache, in Next.js, the `cache` option indicates how a server-side request will interact with the servers Data Cache.
> 

By default, data requests that use `fetch` are **cached**. You can use the [`cache`](/docs/app/building-your-application/caching.html#fetch-optionscache) and [`next.revalidate`](/docs/app/building-your-application/caching.html#fetch-optionsnextrevalidate) options of `fetch` to configure the caching behavior.

**How the Data Cache Works**

![Diagram showing how cached and uncached fetch requests interact with the Data Cache. Cached requests are stored in the Data Cache, and memoized, uncached requests are fetched from the data source, not stored in the Data Cache, and memoized.](/assets/nextjs/13.5/docs/light/data-cache.png)
![Diagram showing how cached and uncached fetch requests interact with the Data Cache. Cached requests are stored in the Data Cache, and memoized, uncached requests are fetched from the data source, not stored in the Data Cache, and memoized.](/assets/nextjs/13.5/docs/dark/data-cache.png)

- The first time a `fetch` request is called during rendering, Next.js checks the Data Cache for a cached response.
- If a cached response is found, it's returned immediately and [memoized](/docs/app/building-your-application/caching.html#request-memoization).
- If a cached response is not found, the request is made to the data source, the result is stored in the Data Cache, and memoized.
- For uncached data (e.g. `{ cache: 'no-store' }`), the result is always fetched from the data source, and memoized.
- Whether the data is cached or uncached, the requests are always memoized to avoid making duplicate requests for the same data during a React render pass.

> **Differences between the Data Cache and Request Memoization**
> While both caching mechanisms help improve performance by re-using cached data, the Data Cache is persistent across incoming requests and deployments, whereas memoization only lasts the lifetime of a request.
> With memoization, we reduce the number of **duplicate** requests in the same render pass that have to cross the network boundary from the rendering server to the Data Cache server (e.g. a CDN or Edge Network) or data source (e.g. a database or CMS). With the Data Cache, we reduce the number of requests made to our origin data source.
> 

### Duration {#duration-1}

The Data Cache is persistent across incoming requests and deployments unless you revalidate or opt-out.

### Revalidating {#revalidating-1}

Cached data can be revalidated in two ways, with:

- **Time-based Revalidation**: Revalidate data after a certain amount of time has passed and a new request is made. This is useful for data that changes infrequently and freshness is not as critical.
- **On-demand Revalidation:** Revalidate data based on an event (e.g. form submission). On-demand revalidation can use a tag-based or path-based approach to revalidate groups of data at once. This is useful when you want to ensure the latest data is shown as soon as possible (e.g. when content from your headless CMS is updated).

#### Time-based Revalidation {#time-based-revalidation}

To revalidate data at a timed interval, you can use the `next.revalidate` option of `fetch` to set the cache lifetime of a resource (in seconds).

```
// Revalidate at most every hour
fetch('https://...', { next: { revalidate: 3600 } })
```

Alternatively, you can use [Route Segment Config options](/docs/app/building-your-application/caching.html#segment-config-options) to configure all `fetch` requests in a segment or for cases where you're not able to use `fetch`.

**How Time-based Revalidation Works**

![Diagram showing how time-based revalidation works, after the revalidation period, stale data is returned for the first request, then data is revalidated.](/assets/nextjs/13.5/docs/light/time-based-revalidation.png)
![Diagram showing how time-based revalidation works, after the revalidation period, stale data is returned for the first request, then data is revalidated.](/assets/nextjs/13.5/docs/dark/time-based-revalidation.png)

- The first time a fetch request with `revalidate` is called, the data will be fetched from the external data source and stored in the Data Cache.
- Any requests that are called within the specified timeframe (e.g. 60-seconds) will return the cached data.
- After the timeframe, the next request will still return the cached (now stale) data.- Next.js will trigger a revalidation of the data in the background.
  - Once the data is fetched successfully, Next.js will update the Data Cache with the fresh data.
  - If the background revalidation fails, the previous data will be kept unaltered.

This is similar to [**stale-while-revalidate**](https://web.dev/stale-while-revalidate/) behavior.

#### On-demand Revalidation {#on-demand-revalidation}

Data can be revalidated on-demand by path ([`revalidatePath`](/docs/app/building-your-application/caching.html#revalidatepath)) or by cache tag ([`revalidateTag`](/docs/app/building-your-application/caching.html#fetch-optionsnexttags-and-revalidatetag)).

**How On-Demand Revalidation Works**

![Diagram showing how on-demand revalidation works, the Data Cache is updated with fresh data after a revalidation request.](/assets/nextjs/13.5/docs/light/on-demand-revalidation.png)
![Diagram showing how on-demand revalidation works, the Data Cache is updated with fresh data after a revalidation request.](/assets/nextjs/13.5/docs/dark/on-demand-revalidation.png)

- The first time a `fetch` request is called, the data will be fetched from the external data source and stored in the Data Cache.
- When an on-demand revalidation is triggered, the appropriate cache entries will be purged from the cache.- This is different from time-based revalidation, which keeps the stale data in the cache until the fresh data is fetched.
- The next time a request is made, it will be a cache `MISS` again, and the data will be fetched from the external data source and stored in the Data Cache.

### Opting out {#opting-out-1}

For individual data fetches, you can opt out of caching by setting the [`cache`](/docs/app/building-your-application/caching.html#fetch-optionscache) option to `no-store`. This means data will be fetched whenever `fetch` is called.

```
// Opt out of caching for an individual `fetch` request
fetch(`https://...`, { cache: 'no-store' })
```

Alternatively, you can also use the [Route Segment Config options](/docs/app/building-your-application/caching.html#segment-config-options) to opt out of caching for a specific route segment. This will affect all data requests in the route segment, including third-party libraries.

```
// Opt out of caching for all data requests in the route segment
export const dynamic = 'force-dynamic'
```

> **Vercel Data Cache**
> If your Next.js application is deployed to Vercel, we recommend reading the [Vercel Data Cache](https://vercel.com/docs/infrastructure/data-cache) documentation for a better understanding of Vercel specific features.
> 

## Full Route Cache {#full-route-cache}

> **Related terms**:
> You may see the terms **Automatic Static Optimization**, **Static Site Generation**, or **Static Rendering** being used interchangeably to refer to the process of rendering and caching routes of your application at build time.
> 

Next.js automatically renders and caches routes at build time. This is an optimization that allows you to serve the cached route instead of rendering on the server for every request, resulting in faster page loads.

To understand how the Full Route Cache works, it's helpful to look at how React handles rendering, and how Next.js caches the result:

### 1. React Rendering on the Server {#1-react-rendering-on-the-server}

On the server, Next.js uses React's APIs to orchestrate rendering. The rendering work is split into chunks: by individual routes segments and Suspense boundaries.

Each chunk is rendered in two steps:

1. React renders Server Components into a special data format, optimized for streaming, called the **React Server Component Payload**.
2. Next.js uses the React Server Component Payload and Client Component JavaScript instructions to render **HTML** on the server.

This means we don't have to wait for everything to render before caching the work or sending a response. Instead, we can stream a response as work is completed.

> **What is the React Server Component Payload?**
> The React Server Component Payload is a compact binary representation of the rendered React Server Components tree. It's used by React on the client to update the browser's DOM. The React Server Component Payload contains:
> - The rendered result of Server Components
> - Placeholders for where Client Components should be rendered and references to their JavaScript files
> - Any props passed from a Server Component to a Client Component
> To learn more, see the [Server Components](/docs/app/building-your-application/rendering/server-components.html) documentation.
> 

### 2. Next.js Caching on the Server (Full Route Cache) {#2-nextjs-caching-on-the-server-full-route-cache}

![Default behavior of the Full Route Cache, showing how the React Server Component Payload and HTML are cached on the server for statically rendered routes.](/assets/nextjs/13.5/docs/light/full-route-cache.png)
![Default behavior of the Full Route Cache, showing how the React Server Component Payload and HTML are cached on the server for statically rendered routes.](/assets/nextjs/13.5/docs/dark/full-route-cache.png)

The default behavior of Next.js is to cache the rendered result (React Server Component Payload and HTML) of a route on the server. This applies to statically rendered routes at build time, or during revalidation.

### 3. React Hydration and Reconciliation on the Client {#3-react-hydration-and-reconciliation-on-the-client}

At request time, on the client:

1. The HTML is used to immediately show a fast non-interactive initial preview of the Client and Server Components.
2. The React Server Components Payload is used to reconcile the Client and rendered Server Component trees, and update the DOM.
3. The JavaScript instructions are used to [hydrate](https://react.dev/reference/react-dom/client/hydrateRoot) Client Components and make the application interactive.

### 4. Next.js Caching on the Client (Router Cache) {#4-nextjs-caching-on-the-client-router-cache}

The React Server Component Payload is stored in the client-side [Router Cache](/docs/app/building-your-application/caching.html#router-cache) - a separate in-memory cache, split by individual route segment. This Router Cache is used to improve the navigation experience by storing previously visited routes and prefetching future routes.

### 5. Subsequent Navigations {#5-subsequent-navigations}

On subsequent navigations or during prefetching, Next.js will check if the React Server Components Payload is stored in Router Cache. If so, it will skip sending a new request to the server.

If the route segments are not in the cache, Next.js will fetch the React Server Components Payload from the server, and populate the Router Cache on the client.

### Static and Dynamic Rendering {#static-and-dynamic-rendering}

Whether a route is cached or not at build time depends on whether it's statically or dynamically rendered. Static routes are cached by default, whereas dynamic routes are rendered at request time, and not cached.

This diagram shows the difference between statically and dynamically rendered routes, with cached and uncached data:

![How static and dynamic rendering affects the Full Route Cache. Static routes are cached at build time or after data revalidation, whereas dynamic routes are never cached](/assets/nextjs/13.5/docs/light/static-and-dynamic-routes.png)
![How static and dynamic rendering affects the Full Route Cache. Static routes are cached at build time or after data revalidation, whereas dynamic routes are never cached](/assets/nextjs/13.5/docs/dark/static-and-dynamic-routes.png)

Learn more about [static and dynamic rendering](/docs/app/building-your-application/rendering/server-components.html#server-rendering-strategies).

### Duration {#duration-2}

By default, the Full Route Cache is persistent. This means that the render output is cached across user requests.

### Invalidation {#invalidation}

There are two ways you can invalidate the Full Route Cache:

- **[Revalidating Data](/docs/app/building-your-application/caching.html#revalidating)**: Revalidating the [Data Cache](/docs/app/building-your-application/caching.html#data-cache), will in turn invalidate the Router Cache by re-rendering components on the server and caching the new render output.
- **Redeploying**: Unlike the Data Cache, which persists across deployments, the Full Route Cache is cleared on new deployments.

### Opting out {#opting-out-2}

You can opt out of the Full Route Cache, or in other words, dynamically render components for every incoming request, by:

- **Using a [Dynamic Function](/docs/app/building-your-application/caching.html#dynamic-functions)**: This will opt the route out from the Full Route Cache and dynamically render it at request time. The Data Cache can still be used.
- **Using the `dynamic = 'force-dynamic'` or `revalidate = 0` route segment config options**: This will skip the Full Route Cache and the Data Cache. Meaning components will be rendered and data fetched on every incoming request to the server. The Router Cache will still apply as it's a client-side cache.
- **Opting out of the [Data Cache](/docs/app/building-your-application/caching.html#data-cache)**: If a route has a `fetch` request that is not cached, this will opt the route out of the Full Route Cache. The data for the specific `fetch` request will be fetched for every incoming request. Other `fetch` requests that do not opt out of caching will still be cached in the Data Cache. This allows for a hybrid of cached and uncached data.

## Router Cache {#router-cache}

> **Related Terms:**
> You may see the Router Cache being referred to as **Client-side Cache** or **Prefetch Cache**. While **Prefetch Cache** refers to the prefetched route segments, **Client-side Cache** refers to the whole Router cache, which includes both visited and prefetched segments.
> This cache specifically applies to Next.js and Server Components, and is different to the browser's [bfcache](https://web.dev/bfcache/), though it has a similar result.
> 

Next.js has an in-memory client-side cache that stores the React Server Component Payload, split by individual route segments, for the duration of a user session. This is called the Router Cache.

**How the Router Cache Works**

![How the Router cache works for static and dynamic routes, showing MISS and HIT for initial and subsequent navigations.](/assets/nextjs/13.5/docs/light/router-cache.png)
![How the Router cache works for static and dynamic routes, showing MISS and HIT for initial and subsequent navigations.](/assets/nextjs/13.5/docs/dark/router-cache.png)

As users navigate between routes, Next.js caches visited route segments and [prefetches](/docs/app/building-your-application/routing/linking-and-navigating.html#1-prefetching) the routes the user is likely to navigate to (based on `<Link>` components in their viewport).

This results in an improved navigation experience for the user:

- Instant backward/forward navigation because visited routes are cached and fast navigation to new routes because of prefetching and [partial rendering](/docs/app/building-your-application/routing/linking-and-navigating.html#3-partial-rendering).
- No full-page reload between navigations and React state and browser state is preserved.

> **Difference between the Router Cache and Full Route Cache**:
> The Router Cache temporarily stores the React Server Component Payload in the browser for the duration of a user session, whereas the Full Route Cache persistently stores the React Server Component Payload and HTML on the server across multiple user requests.
> While the Full Route Cache only caches statically rendered routes, the Router Cache applies to both statically and dynamically rendered routes.
> 

### Duration {#duration-3}

The cache is stored in the browser's temporary memory. Two factors determine how long the router cache lasts:

- **Session**: The cache persists across navigation. However, it's cleared on page refresh.
- **Automatic Invalidation Period**: The cache of an individual segment is automatically invalidated after a specific time. The duration depends on whether the route is [statically](/docs/app/building-your-application/rendering/server-components.html#static-rendering-default) or [dynamically](/docs/app/building-your-application/rendering/server-components.html#dynamic-rendering) rendered:- **Dynamically Rendered**: 30 seconds
  - **Statically Rendered**: 5 minutes

While a page refresh will clear **all** cached segments, the automatic invalidation period only affects the individual segment from the time it was last accessed or created.

By adding `prefetch={true}` or calling `router.prefetch` for a dynamically rendered route, you can opt into caching for 5 minutes.

### Invalidation {#invalidation-1}

There are two ways you can invalidate the Router Cache:

- In a **Server Action**:- Revalidating data on-demand by path with ([`revalidatePath`](/docs/app/api-reference/functions/revalidatePath.html)) or by cache tag with ([`revalidateTag`](/docs/app/api-reference/functions/revalidateTag.html))
  - Using [`cookies.set`](/docs/app/api-reference/functions/cookies.html#cookiessetname-value-options) or [`cookies.delete`](/docs/app/api-reference/functions/cookies.html#deleting-cookies) invalidates the Router Cache to prevent routes that use cookies from becoming stale (e.g. authentication).
- Calling [`router.refresh`](/docs/app/api-reference/functions/use-router.html) will invalidate the Router Cache and make a new request to the server for the current route.

### Opting out {#opting-out-3}

It's not possible to opt out of the Router Cache.

You can opt out of **prefetching** by setting the `prefetch` prop of the `<Link>` component to `false`. However, this will still temporarily store the route segments for 30s to allow instant navigation between nested segments, such as tab bars, or back and forward navigation. Visited routes will still be cached.

## Cache Interactions {#cache-interactions}

When configuring the different caching mechanisms, it's important to understand how they interact with each other:

### Data Cache and Full Route Cache {#data-cache-and-full-route-cache}

- Revalidating or opting out of the Data Cache **will** invalidate the Full Route Cache, as the render output depends on data.
- Invalidating or opting out of the Full Route Cache **does not** affect the Data Cache. You can dynamically render a route that has both cached and uncached data. This is useful when most of your page uses cached data, but you have a few components that rely on data that needs to be fetched at request time. You can dynamically render without worrying about the performance impact of re-fetching all the data.

### Data Cache and Client-side Router cache {#data-cache-and-client-side-router-cache}

- Revalidating the Data Cache in a [Route Handler](/docs/app/building-your-application/routing/route-handlers.html)**will not** immediately invalidate the Router Cache as the Router Handler isn't tied to a specific route. This means Router Cache will continue to serve the previous payload until a hard refresh, or the automatic invalidation period has elapsed.
- To immediately invalidate the Data Cache and Router cache, you can use [`revalidatePath`](/docs/app/building-your-application/caching.html#revalidatepath) or [`revalidateTag`](/docs/app/building-your-application/caching.html#fetch-optionsnexttags-and-revalidatetag) in a [Server Action](/docs/app/building-your-application/data-fetching/forms-and-mutations.html).

## APIs {#apis}

The following table provides an overview of how different Next.js APIs affect caching:

|API|Router Cache|Full Route Cache|Data Cache|React Cache|
|---|---|---|---|---|
|[`<Link prefetch>`](/docs/app/building-your-application/caching.html#link)|Cache||||
|[`router.prefetch`](/docs/app/building-your-application/caching.html#routerprefetch)|Cache||||
|[`router.refresh`](/docs/app/building-your-application/caching.html#routerrefresh)|Revalidate||||
|[`fetch`](/docs/app/building-your-application/caching.html#fetch)|||Cache|Cache|
|[`fetch``options.cache`](/docs/app/building-your-application/caching.html#fetch-optionscache)|||Cache or Opt out||
|[`fetch``options.next.revalidate`](/docs/app/building-your-application/caching.html#fetch-optionsnextrevalidate)||Revalidate|Revalidate||
|[`fetch``options.next.tags`](/docs/app/building-your-application/caching.html#fetch-optionsnexttags-and-revalidatetag)||Cache|Cache||
|[`revalidateTag`](/docs/app/building-your-application/caching.html#fetch-optionsnexttags-and-revalidatetag)|Revalidate (Server Action)|Revalidate|Revalidate||
|[`revalidatePath`](/docs/app/building-your-application/caching.html#revalidatepath)|Revalidate (Server Action)|Revalidate|Revalidate||
|[`const revalidate`](/docs/app/building-your-application/caching.html#segment-config-options)||Revalidate or Opt out|Revalidate or Opt out||
|[`const dynamic`](/docs/app/building-your-application/caching.html#segment-config-options)||Cache or Opt out|Cache or Opt out||
|[`cookies`](/docs/app/building-your-application/caching.html#cookies)|Revalidate (Server Action)|Opt out|||
|[`headers`, `useSearchParams`, `searchParams`](/docs/app/building-your-application/caching.html#dynamic-functions)||Opt out|||
|[`generateStaticParams`](/docs/app/building-your-application/caching.html#generatestaticparams)||Cache|||
|[`React.cache`](/docs/app/building-your-application/caching.html#react-cache-function)||||Cache|
|[`unstable_cache`](/docs/app/building-your-application/caching.html#unstable_cache) (Coming Soon)|||||


### `<Link>` {#link}

By default, the `<Link>` component automatically prefetches routes from the Full Route Cache and adds the React Server Component Payload to the Router Cache.

To disable prefetching, you can set the `prefetch` prop to `false`. But this will not skip the cache permanently, the route segment will still be cached client-side when the user visits the route.

Learn more about the [`<Link>` component](/docs/app/api-reference/components/link.html).

### `router.prefetch` {#routerprefetch}

The `prefetch` option of the `useRouter` hook can be used to manually prefetch a route. This adds the React Server Component Payload to the Router Cache.

See the [`useRouter` hook](/docs/app/api-reference/functions/use-router.html) API reference.

### `router.refresh` {#routerrefresh}

The `refresh` option of the `useRouter` hook can be used to manually refresh a route. This completely clears the Router Cache, and makes a new request to the server for the current route. `refresh` does not affect the Data or Full Route Cache.

The rendered result will be reconciled on the client while preserving React state and browser state.

See the [`useRouter` hook](/docs/app/api-reference/functions/use-router.html) API reference.

### `fetch` {#fetch}

Data returned from `fetch` is automatically cached in the Data Cache.

```
// Cached by default. `force-cache` is the default option and can be ommitted.
fetch(`https://...`, { cache: 'force-cache' })
```

See the [`fetch` API Reference](/docs/app/api-reference/functions/fetch.html) for more options.

### `fetch options.cache` {#fetch-optionscache}

You can opt out individual `fetch` requests of data caching by setting the `cache` option to `no-store`:

```
// Opt out of caching
fetch(`https://...`, { cache: 'no-store' })
```

Since the render output depends on data, using `cache: 'no-store'` will also skip the Full Route Cache for the route where the `fetch` request is used. That is, the route will be dynamically rendered every request, but you can still have other cached data requests in the same route.

See the [`fetch` API Reference](/docs/app/api-reference/functions/fetch.html) for more options.

### `fetch options.next.revalidate` {#fetch-optionsnextrevalidate}

You can use the `next.revalidate` option of `fetch` to set the revalidation period (in seconds) of an individual `fetch` request. This will revalidate the Data Cache, which in turn will revalidate the Full Route Cache. Fresh data will be fetched, and components will be re-rendered on the server.

```
// Revalidate at most after 1 hour
fetch(`https://...`, { next: { revalidate: 3600 } })
```

See the [`fetch` API reference](/docs/app/api-reference/functions/fetch.html) for more options.

### `fetch options.next.tags` and `revalidateTag` {#fetch-optionsnexttags-and-revalidatetag}

Next.js has a cache tagging system for fine-grained data caching and revalidation.

1. When using `fetch` or `unstable_cache`, you have the option to tag cache entries with one or more tags.
2. Then, you can call `revalidateTag` to purge the cache entries associated with that tag.

For example, you can set a tag when fetching data:

```
// Cache data with a tag
fetch(`https://...`, { next: { tags: ['a', 'b', 'c'] } })
```

Then, call `revalidateTag` with a tag to purge the cache entry:

```
// Revalidate entries with a specific tag
revalidateTag('a')
```

There are two places you can use `revalidateTag`, depending on what you're trying to achieve:

1. [Route Handlers](/docs/app/building-your-application/routing/route-handlers.html) - to revalidate data in response of a third party event (e.g. webhook). This will not invalidate the Router Cache immediately as the Router Handler isn't tied to a specific route.
2. [Server Actions](/docs/app/building-your-application/data-fetching/forms-and-mutations.html) - to revalidate data after a user action (e.g. form submission). This will invalidate the Router Cache for the associated route.

### `revalidatePath` {#revalidatepath}

`revalidatePath` allows you manually revalidate data **and** re-render the route segments below a specific path in a single operation. Calling `revalidatePath` methods revalidate the Data Cache, which in turn invalidates the Full Route Cache.

```
revalidatePath('/')
```

There are two places you can use `revalidatePath`, depending on what you're trying to achieve:

1. [Route Handlers](/docs/app/building-your-application/routing/route-handlers.html) - to revalidate data in response to a third party event (e.g. webhook).
2. [Server Actions](/docs/app/building-your-application/data-fetching/forms-and-mutations.html) - to revalidate data after a user interaction (e.g. form submission, clicking a button).

See the [`revalidatePath` API reference](/docs/app/api-reference/functions/revalidatePath.html) for more information.

> **`revalidatePath`** vs. **`router.refresh`**:
> Calling `router.refresh` will clear the Router cache, and re-render route segments on the server without invalidating the Data Cache or the Full Route Cache.
> The difference is that `revalidatePath` purges the Data Cache and Full Route Cache, whereas `router.refresh()` does not change the Data Cache and Full Route Cache, as it is a client-side API.
> 

### Dynamic Functions {#dynamic-functions}

`cookies`, `headers`, `useSearchParams`, and `searchParams` are all dynamic functions that depend on runtime incoming request information. Using them will opt a route out of the Full Route Cache, in other words, the route will be dynamically rendered.

#### `cookies` {#cookies}

Using `cookies.set` or `cookies.delete` in a Server Action invalidates the Router Cache to prevent routes that use cookies from becoming stale (e.g. to reflect authentication changes).

See the [`cookies`](/docs/app/api-reference/functions/cookies.html) API reference.

### Segment Config Options {#segment-config-options}

The Route Segment Config options can be used to override the route segment defaults or when you're not able to use the `fetch` API (e.g. database client or 3rd party libraries).

The following Route Segment Config options will opt out of the Data Cache and Full Route Cache:

- `const dynamic = 'force-dynamic'`
- `const revalidate = 0`

See the [Route Segment Config](/docs/app/api-reference/file-conventions/route-segment-config.html) documentation for more options.

### `generateStaticParams` {#generatestaticparams}

For [dynamic segments](/docs/app/building-your-application/routing/dynamic-routes.html) (e.g. `app/blog/[slug]/page.js`), paths provided by `generateStaticParams` are cached in the Full Route Cache at build time. At request time, Next.js will also cache paths that weren't known at build time the first time they're visited.

You can disable caching at request time by using `export const dynamicParams = false` option in a route segment. When this config option is used, only paths provided by `generateStaticParams` will be served, and other routes will 404 or match (in the case of [catch-all routes](/docs/app/building-your-application/routing/dynamic-routes.html#catch-all-segments)).

See the [`generateStaticParams` API reference](/docs/app/api-reference/functions/generate-static-params.html).

### React `cache` function {#react-cache-function}

The React `cache` function allows you to memoize the return value of a function, allowing you to call the same function multiple times while only executing it once.

Since `fetch` requests are automatically memoized, you do not need to wrap it in React `cache`. However, you can use `cache` to manually memoize data requests for use cases when the `fetch` API is not suitable. For example, some database clients, CMS clients, or GraphQL clients.


utils/get-item.ts
```
import { cache } from 'react'
import db from '@/lib/db'
 
export const getItem = cache(async (id: string) => {
  const item = await db.item.findUnique({ id })
  return item
})
```

### `unstable_cache` {#unstable_cache}

`unstable_cache` is an experimental API for adding values to the Data Cache when the `fetch` API is not suitable. For example, when using database clients, CMS clients, or GraphQL.

```
import { unstable_cache } from 'next/cache'
 
export default async function Page() {
  const cachedData = await unstable_cache(
    async () => {
      const data = await db.query('...')
      return data
    },
    ['cache-key'],
    {
      tags: ['a', 'b', 'c'],
      revalidate: 10,
    }
  )()
}
```

> **Warning**: This API is being developed, and we do not recommend using it in production. It's listed here to show the future direction of the Data Cache.
> 
