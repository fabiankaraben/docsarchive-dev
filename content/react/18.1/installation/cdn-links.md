---
linkTitle: "CDN Links"
title: "CDN Links – React"
description: ""
weight: 4
type: docs
---

# CDN Links

Both React and ReactDOM are available over a CDN.

```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
```

The versions above are only meant for development, and are not suitable for production. Minified and optimized production versions of React are available at:

```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
```

To load a specific version of `react` and `react-dom`, replace `18` with the version number.

### Why the `crossorigin` Attribute? {#why-the-crossorigin-attribute}

If you serve React from a CDN, we recommend to keep the [`crossorigin` ↗](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_settings_attributes) attribute set:

```html
<script crossorigin src="..."></script>
```

We also recommend to verify that the CDN you are using sets the `Access-Control-Allow-Origin: *` HTTP header:

[![Access-Control-Allow-Origin: *](/assets/react/18.1/static/89baed0a6540f29e954065ce04661048/13ae7/cdn-cors-header.png)](/static/89baed0a6540f29e954065ce04661048/13ae7/cdn-cors-header.png)

This enables a better [error handling experience](/blog/2017/07/26/error-handling-in-react-16.html) in React 16 and later.
