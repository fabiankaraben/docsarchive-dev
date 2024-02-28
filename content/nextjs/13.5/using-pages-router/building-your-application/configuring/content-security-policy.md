---
linkTitle: "Content Security Policy"
title: "Configuring: Content Security Policy | Next.js"
description: "Learn how to set a Content Security Policy (CSP) for your Next.js application."
weight: 15
type: docs
---

# Content Security Policy

[Content Security Policy (CSP) ↗](https://developer.mozilla.org/docs/Web/HTTP/CSP) is important to guard your Next.js application against various security threats such as cross-site scripting (XSS), clickjacking, and other code injection attacks.

By using CSP, developers can specify which origins are permissible for content sources, scripts, stylesheets, images, fonts, objects, media (audio, video), iframes, and more.

## Nonces {#nonces}

A [nonce ↗](https://developer.mozilla.org/docs/Web/HTML/Global_attributes/nonce) is a unique, random string of characters created for a one-time use. It is used in conjunction with CSP to selectively allow certain inline scripts or styles to execute, bypassing strict CSP directives.

### Why use a nonce? {#why-use-a-nonce}

Even though CSPs are designed to block malicious scripts, there are legitimate scenarios where inline scripts are necessary. In such cases, nonces offer a way to allow these scripts to execute if they have the correct nonce.

### Adding a nonce with Middleware {#adding-a-nonce-with-middleware}

[Middleware](/nextjs/13.5/using-app-router/building-your-application/routing/middleware) enables you to add headers and generate nonces before the page renders.

Every time a page is viewed, a fresh nonce should be generated. This means that you **must use dynamic rendering to add nonces**.

For example:


middleware.ts
```
import { NextRequest, NextResponse } from 'next/server'
 
export function middleware(request: NextRequest) {
  const nonce = Buffer.from(crypto.randomUUID()).toString('base64')
  const cspHeader = `
    default-src 'self';
    script-src 'self' 'nonce-${nonce}' 'strict-dynamic';
    style-src 'self' 'nonce-${nonce}';
    img-src 'self' blob: data:;
    font-src 'self';
    object-src 'none';
    base-uri 'self';
    form-action 'self';
    frame-ancestors 'none';
    block-all-mixed-content;
    upgrade-insecure-requests;
`
 
  const requestHeaders = new Headers(request.headers)
  requestHeaders.set('x-nonce', nonce)
  requestHeaders.set(
    'Content-Security-Policy',
    // Replace newline characters and spaces
    cspHeader.replace(/\s{2,}/g, ' ').trim()
  )
 
  return NextResponse.next({
    headers: requestHeaders,
    request: {
      headers: requestHeaders,
    },
  })
}
```

By default, Middleware runs on all requests. You can filter Middleware to run on specific paths using a [`matcher`](/nextjs/13.5/using-app-router/building-your-application/routing/middleware#matcher).

We recommend ignoring matching prefetches (from `next/link`) and static assets that don't need the CSP header.


middleware.ts
```
export const config = {
  matcher: [
    /*
     * Match all request paths except for the ones starting with:
     * - api (API routes)
     * - _next/static (static files)
     * - _next/image (image optimization files)
     * - favicon.ico (favicon file)
     */
    {
      source: '/((?!api|_next/static|_next/image|favicon.ico).*)',
      missing: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },
  ],
}
```

### Reading the nonce {#reading-the-nonce}

You can now read the nonce from a [Server Component ↗](https://nextjs.org/docs/app/building-your-application/rendering/server-components.html) using [`headers`](/nextjs/13.5/using-app-router/api-reference/functions/headers):


app/page.tsx
```
import { headers } from 'next/headers'
import Script from 'next/script'
 
export default function Page() {
  const nonce = headers().get('x-nonce')
 
  return (
    <Script
      src="https://www.googletagmanager.com/gtag/js"
      strategy="afterInteractive"
      nonce={nonce}
    />
  )
}
```

## Version History {#version-history}

We recommend using `v13.4.20+` of Next.js to properly handle and apply nonces.
