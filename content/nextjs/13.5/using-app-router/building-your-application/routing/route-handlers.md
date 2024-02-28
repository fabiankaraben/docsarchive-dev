---
linkTitle: "Route Handlers"
title: "Routing: Route Handlers | Next.js"
description: "Create custom request handlers for a given route using the Web's Request and Response APIs."
weight: 10
type: docs
---

# Route Handlers

Route Handlers allow you to create custom request handlers for a given route using the Web [Request ↗](https://developer.mozilla.org/docs/Web/API/Request) and [Response ↗](https://developer.mozilla.org/docs/Web/API/Response) APIs.

![Route.js Special File](/assets/nextjs/13.5/docs/light/route-special-file.png)
![Route.js Special File](/assets/nextjs/13.5/docs/dark/route-special-file.png)

> **Good to know**: Route Handlers are only available inside the `app` directory. They are the equivalent of [API Routes](/docs/pages/building-your-application/routing/api-routes.html) inside the `pages` directory meaning you **do not** need to use API Routes and Route Handlers together.
> 

## Convention {#convention}

Route Handlers are defined in a [`route.js|ts` file](/docs/app/api-reference/file-conventions/route.html) inside the `app` directory:


app/api/route.ts
```
export async function GET(request: Request) {}
```

Route Handlers can be nested inside the `app` directory, similar to `page.js` and `layout.js`. But there **cannot** be a `route.js` file at the same route segment level as `page.js`.

### Supported HTTP Methods {#supported-http-methods}

The following [HTTP methods](https://developer.mozilla.org/docs/Web/HTTP/Methods) are supported: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, and `OPTIONS`. If an unsupported method is called, Next.js will return a `405 Method Not Allowed` response.

### Extended `NextRequest` and `NextResponse` APIs {#extended-nextrequest-and-nextresponse-apis}

In addition to supporting native [Request](https://developer.mozilla.org/docs/Web/API/Request) and [Response](https://developer.mozilla.org/docs/Web/API/Response). Next.js extends them with
[`NextRequest`](/docs/app/api-reference/functions/next-request.html) and [`NextResponse`](/docs/app/api-reference/functions/next-response.html) to provide convenient helpers for advanced use cases.

## Behavior {#behavior}

### Caching {#caching}

Route Handlers are cached by default when using the `GET` method with the `Response` object.


app/items/route.ts
```
export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  const data = await res.json()
 
  return Response.json({ data })
}
```

> **TypeScript Warning:**`Response.json()` is only valid from TypeScript 5.2. If you use a lower TypeScript version, you can use [`NextResponse.json()`](/docs/app/api-reference/functions/next-response.html#json) for typed responses instead.
> 

### Opting out of caching {#opting-out-of-caching}

You can opt out of caching by:

- Using the `Request` object with the `GET` method.
- Using any of the other HTTP methods.
- Using [Dynamic Functions](/docs/app/building-your-application/routing/route-handlers.html#dynamic-functions) like `cookies` and `headers`.
- The [Segment Config Options](/docs/app/building-your-application/routing/route-handlers.html#segment-config-options) manually specifies dynamic mode.

For example:


app/products/api/route.ts
```
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const id = searchParams.get('id')
  const res = await fetch(`https://data.mongodb-api.com/product/${id}`, {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  const product = await res.json()
 
  return Response.json({ product })
}
```

Similarly, the `POST` method will cause the Route Handler to be evaluated dynamically.


app/items/route.ts
```
export async function POST() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
    body: JSON.stringify({ time: new Date().toISOString() }),
  })
 
  const data = await res.json()
 
  return Response.json(data)
}
```

> **Good to know**: Like API Routes, Route Handlers can be used for cases like handling form submissions. A new abstraction for [handling forms and mutations](/docs/app/building-your-application/data-fetching/forms-and-mutations.html) that integrates deeply with React is being worked on.
> 

### Route Resolution {#route-resolution}

You can consider a `route` the lowest level routing primitive.

- They **do not** participate in layouts or client-side navigations like `page`.
- There **cannot** be a `route.js` file at the same route as `page.js`.

|Page|Route|Result|
|---|---|---|
|`app/page.js`|`app/route.js`| Conflict|
|`app/page.js`|`app/api/route.js`| Valid|
|`app/[user]/page.js`|`app/api/route.js`| Valid|


Each `route.js` or `page.js` file takes over all HTTP verbs for that route.


app/page.js
```
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
 
// ❌ Conflict
// `app/route.js`
export async function POST(request) {}
```

## Examples {#examples}

The following examples show how to combine Route Handlers with other Next.js APIs and features.

### Revalidating Cached Data {#revalidating-cached-data}

You can [revalidate cached data](/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html#revalidating-data) using the [`next.revalidate`](/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html#revalidating-data) option:


app/items/route.ts
```
export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    next: { revalidate: 60 }, // Revalidate every 60 seconds
  })
  const data = await res.json()
 
  return Response.json(data)
}
```

Alternatively, you can use the [`revalidate` segment config option](/docs/app/api-reference/file-conventions/route-segment-config.html#revalidate):

```
export const revalidate = 60
```

### Dynamic Functions {#dynamic-functions}

Route Handlers can be used with dynamic functions from Next.js, like [`cookies`](/docs/app/api-reference/functions/cookies.html) and [`headers`](/docs/app/api-reference/functions/headers.html).

#### Cookies {#cookies}

You can read cookies with [`cookies`](/docs/app/api-reference/functions/cookies.html) from `next/headers`. This server function can be called directly in a Route Handler, or nested inside of another function.

This `cookies` instance is read-only. To set cookies, you need to return a new `Response` using the [`Set-Cookie`](https://developer.mozilla.org/docs/Web/HTTP/Headers/Set-Cookie) header.


app/api/route.ts
```
import { cookies } from 'next/headers'
 
export async function GET(request: Request) {
  const cookieStore = cookies()
  const token = cookieStore.get('token')
 
  return new Response('Hello, Next.js!', {
    status: 200,
    headers: { 'Set-Cookie': `token=${token.value}` },
  })
}
```

Alternatively, you can use abstractions on top of the underlying Web APIs to read cookies ([`NextRequest`](/docs/app/api-reference/functions/next-request.html)):


app/api/route.ts
```
import { type NextRequest } from 'next/server'
 
export async function GET(request: NextRequest) {
  const token = request.cookies.get('token')
}
```

#### Headers {#headers}

You can read headers with [`headers`](/docs/app/api-reference/functions/headers.html) from `next/headers`. This server function can be called directly in a Route Handler, or nested inside of another function.

This `headers` instance is read-only. To set headers, you need to return a new `Response` with new `headers`.


app/api/route.ts
```
import { headers } from 'next/headers'
 
export async function GET(request: Request) {
  const headersList = headers()
  const referer = headersList.get('referer')
 
  return new Response('Hello, Next.js!', {
    status: 200,
    headers: { referer: referer },
  })
}
```

Alternatively, you can use abstractions on top of the underlying Web APIs to read headers ([`NextRequest`](/docs/app/api-reference/functions/next-request.html)):


app/api/route.ts
```
import { type NextRequest } from 'next/server'
 
export async function GET(request: NextRequest) {
  const requestHeaders = new Headers(request.headers)
}
```

### Redirects {#redirects}


app/api/route.ts
```
import { redirect } from 'next/navigation'
 
export async function GET(request: Request) {
  redirect('https://nextjs.org/')
}
```

### Dynamic Route Segments {#dynamic-route-segments}

> We recommend reading the [Defining Routes](/docs/app/building-your-application/routing/defining-routes.html) page before continuing.
> 

Route Handlers can use [Dynamic Segments](/docs/app/building-your-application/routing/dynamic-routes.html) to create request handlers from dynamic data.


app/items/[slug]/route.ts
```
export async function GET(
  request: Request,
  { params }: { params: { slug: string } }
) {
  const slug = params.slug // 'a', 'b', or 'c'
}
```

|Route|Example URL|`params`|
|---|---|---|
|`app/items/[slug]/route.js`|`/items/a`|`{ slug: 'a' }`|
|`app/items/[slug]/route.js`|`/items/b`|`{ slug: 'b' }`|
|`app/items/[slug]/route.js`|`/items/c`|`{ slug: 'c' }`|


### URL Query Parameters {#url-query-parameters}

The request object passed to the Route Handler is a `NextRequest` instance, which has [some additional convenience methods](/docs/app/api-reference/functions/next-request.html#nexturl), including for more easily handling query parameters.


app/api/search/route.ts
```
import { type NextRequest } from 'next/server'
 
export function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const query = searchParams.get('query')
  // query is "hello" for /api/search?query=hello
}
```

### Streaming {#streaming}

Streaming is commonly used in combination with Large Language Models (LLMs), such as OpenAI, for AI-generated content. Learn more about the [AI SDK](https://sdk.vercel.ai/docs).


app/api/chat/route.ts
```
import { Configuration, OpenAIApi } from 'openai-edge'
import { OpenAIStream, StreamingTextResponse } from 'ai'
 
export const runtime = 'edge'
 
const apiConfig = new Configuration({
  apiKey: process.env.OPENAI_API_KEY!,
})
 
const openai = new OpenAIApi(apiConfig)
 
export async function POST(req: Request) {
  // Extract the `messages` from the body of the request
  const { messages } = await req.json()
 
  // Request the OpenAI API for the response based on the prompt
  const response = await openai.createChatCompletion({
    model: 'gpt-3.5-turbo',
    stream: true,
    messages: messages,
    max_tokens: 500,
    temperature: 0.7,
    top_p: 1,
    frequency_penalty: 1,
    presence_penalty: 1,
  })
 
  // Convert the response into a friendly text-stream
  const stream = OpenAIStream(response)
 
  // Respond with the stream
  return new StreamingTextResponse(stream)
}
```

These abstractions use the Web APIs to create a stream. You can also use the underlying Web APIs directly.


app/api/route.ts
```
// https://developer.mozilla.org/docs/Web/API/ReadableStream#convert_async_iterator_to_stream
function iteratorToStream(iterator: any) {
  return new ReadableStream({
    async pull(controller) {
      const { value, done } = await iterator.next()
 
      if (done) {
        controller.close()
      } else {
        controller.enqueue(value)
      }
    },
  })
}
 
function sleep(time: number) {
  return new Promise((resolve) => {
    setTimeout(resolve, time)
  })
}
 
const encoder = new TextEncoder()
 
async function* makeIterator() {
  yield encoder.encode('<p>One</p>')
  await sleep(200)
  yield encoder.encode('<p>Two</p>')
  await sleep(200)
  yield encoder.encode('<p>Three</p>')
}
 
export async function GET() {
  const iterator = makeIterator()
  const stream = iteratorToStream(iterator)
 
  return new Response(stream)
}
```

### Request Body {#request-body}

You can read the `Request` body using the standard Web API methods:


app/items/route.ts
```
export async function POST(request: Request) {
  const res = await request.json()
  return Response.json({ res })
}
```

### Request Body FormData {#request-body-formdata}

You can read the `FormData` using the `request.formData()` function:


app/items/route.ts
```
export async function POST(request: Request) {
  const formData = await request.formData()
  const name = formData.get('name')
  const email = formData.get('email')
  return Response.json({ name, email })
}
```

Since `formData` data are all strings, you may want to use [`zod-form-data`](https://www.npmjs.com/zod-form-data) to validate the request and retrieve data in the format you prefer (e.g. `number`).

### CORS {#cors}

You can set CORS headers on a `Response` using the standard Web API methods:


app/api/route.ts
```
export async function GET(request: Request) {
  return new Response('Hello, Next.js!', {
    status: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
    },
  })
}
```

### Edge and Node.js Runtimes {#edge-and-nodejs-runtimes}

Route Handlers have an isomorphic Web API to support both Edge and Node.js runtimes seamlessly, including support for streaming. Since Route Handlers use the same [route segment configuration](/docs/app/api-reference/file-conventions/route-segment-config.html) as Pages and Layouts, they support long-awaited features like general-purpose [statically regenerated](/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating.html#revalidating-data) Route Handlers.

You can use the `runtime` segment config option to specify the runtime:

```
export const runtime = 'edge' // 'nodejs' is the default
```

### Non-UI Responses {#non-ui-responses}

You can use Route Handlers to return non-UI content. Note that [`sitemap.xml`](/docs/app/api-reference/file-conventions/metadata/sitemap.html#generate-a-sitemap), [`robots.txt`](/docs/app/api-reference/file-conventions/metadata/robots.html#generate-a-robots-file), [`app icons`](/docs/app/api-reference/file-conventions/metadata/app-icons.html#generate-icons-using-code-js-ts-tsx), and [open graph images](/docs/app/api-reference/file-conventions/metadata/opengraph-image.html) all have built-in support.


app/rss.xml/route.ts
```
export async function GET() {
  return new Response(`<?xml version="1.0" encoding="UTF-8" ?>
<rss version="2.0">
 
<channel>
  <title>Next.js Documentation</title>
  <link>https://nextjs.org/docs</link>
  <description>The React Framework for the Web</description>
</channel>
 
</rss>`)
}
```

### Segment Config Options {#segment-config-options}

Route Handlers use the same [route segment configuration](/docs/app/api-reference/file-conventions/route-segment-config.html) as pages and layouts.


app/items/route.ts
```
export const dynamic = 'auto'
export const dynamicParams = true
export const revalidate = false
export const fetchCache = 'auto'
export const runtime = 'nodejs'
export const preferredRegion = 'auto'
```

See the [API reference](/docs/app/api-reference/file-conventions/route-segment-config.html) for more details.
