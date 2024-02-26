---
linkTitle: "ReactDOMServer"
title: "ReactDOMServer – React"
description: ""
weight: 4
type: docs
---

# ReactDOMServer

The `ReactDOMServer` object enables you to render components to static markup. Typically, it’s used on a Node server:

```jsx
// ES modules
import * as ReactDOMServer from 'react-dom/server';
// CommonJS
var ReactDOMServer = require('react-dom/server');
```

## Overview {#overview}

These methods are only available in the **environments with [Node.js Streams ↗](https://nodejs.dev/learn/nodejs-streams):**

- [`renderToPipeableStream()`](/react/18.1/api-reference/react-dom-server#rendertopipeablestream)
- [`renderToNodeStream()`](/react/18.1/api-reference/react-dom-server#rendertonodestream) (Deprecated)
- [`renderToStaticNodeStream()`](/react/18.1/api-reference/react-dom-server#rendertostaticnodestream)

These methods are only available in the **environments with [Web Streams ↗](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API)** (this includes browsers, Deno, and some modern edge runtimes):

- [`renderToReadableStream()`](/react/18.1/api-reference/react-dom-server#rendertoreadablestream)

The following methods can be used in the environments that don’t support streams:

- [`renderToString()`](/react/18.1/api-reference/react-dom-server#rendertostring)
- [`renderToStaticMarkup()`](/react/18.1/api-reference/react-dom-server#rendertostaticmarkup)

## Reference {#reference}

### `renderToPipeableStream()` {#rendertopipeablestream}

```jsx
ReactDOMServer.renderToPipeableStream(element, options)
```

Render a React element to its initial HTML. Returns a stream with a `pipe(res)` method to pipe the output and `abort()` to abort the request. Fully supports Suspense and streaming of HTML with “delayed” content blocks “popping in” via inline `<script>` tags later. [Read more ↗](https://github.com/reactwg/react-18/discussions/37)

If you call [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on a node that already has this server-rendered markup, React will preserve it and only attach event handlers, allowing you to have a very performant first-load experience.

```jsx
let didError = false;
const stream = renderToPipeableStream(
  <App />,
  {
    onShellReady() {
      // The content above all Suspense boundaries is ready.
      // If something errored before we started streaming, we set the error code appropriately.
      res.statusCode = didError ? 500 : 200;
      res.setHeader('Content-type', 'text/html');
      stream.pipe(res);
    },
    onShellError(error) {
      // Something errored before we could complete the shell so we emit an alternative shell.
      res.statusCode = 500;
      res.send(
        '<!doctype html><p>Loading...</p><script src="clientrender.js"></script>'
      );
    },
    onAllReady() {
      // If you don't want streaming, use this instead of onShellReady.
      // This will fire after the entire page content is ready.
      // You can use this for crawlers or static generation.

      // res.statusCode = didError ? 500 : 200;
      // res.setHeader('Content-type', 'text/html');
      // stream.pipe(res);
    },
    onError(err) {
      didError = true;
      console.error(err);
    },
  }
);
```

See the [full list of options ↗](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-dom/src/server/ReactDOMFizzServerNode.js#L36-L46).

> Note:
> This is a Node.js-specific API. Environments with [Web Streams ↗](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), like Deno and modern edge runtimes, should use [`renderToReadableStream`](/react/18.1/api-reference/react-dom-server#rendertoreadablestream) instead.
> 

---

### `renderToReadableStream()` {#rendertoreadablestream}

```jsx
ReactDOMServer.renderToReadableStream(element, options);
```

Streams a React element to its initial HTML. Returns a Promise that resolves to a [Readable Stream ↗](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream). Fully supports Suspense and streaming of HTML. [Read more ↗](https://github.com/reactwg/react-18/discussions/127)

If you call [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on a node that already has this server-rendered markup, React will preserve it and only attach event handlers, allowing you to have a very performant first-load experience.

```jsx
let controller = new AbortController();
let didError = false;
try {
  let stream = await renderToReadableStream(
    <html>
      <body>Success</body>
    </html>,
    {
      signal: controller.signal,
      onError(error) {
        didError = true;
        console.error(error);
      }
    }
  );
  
  // This is to wait for all Suspense boundaries to be ready. You can uncomment
  // this line if you want to buffer the entire HTML instead of streaming it.
  // You can use this for crawlers or static generation:

  // await stream.allReady;

  return new Response(stream, {
    status: didError ? 500 : 200,
    headers: {'Content-Type': 'text/html'},
  });
} catch (error) {
  return new Response(
    '<!doctype html><p>Loading...</p><script src="clientrender.js"></script>',
    {
      status: 500,
      headers: {'Content-Type': 'text/html'},
    }
  );
}
```

See the [full list of options ↗](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-dom/src/server/ReactDOMFizzServerBrowser.js#L27-L35).

> Note:
> This API depends on [Web Streams ↗](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API). For Node.js, use [`renderToPipeableStream`](/react/18.1/api-reference/react-dom-server#rendertopipeablestream) instead.
> 

---

### `renderToNodeStream()`  (Deprecated) {#rendertonodestream}

```jsx
ReactDOMServer.renderToNodeStream(element)
```

Render a React element to its initial HTML. Returns a [Node.js Readable stream ↗](https://nodejs.org/api/stream.html#stream_readable_streams) that outputs an HTML string. The HTML output by this stream is exactly equal to what [`ReactDOMServer.renderToString`](/react/18.1/api-reference/react-dom-server#rendertostring) would return. You can use this method to generate HTML on the server and send the markup down on the initial request for faster page loads and to allow search engines to crawl your pages for SEO purposes.

If you call [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on a node that already has this server-rendered markup, React will preserve it and only attach event handlers, allowing you to have a very performant first-load experience.

> Note:
> Server-only. This API is not available in the browser.
> The stream returned from this method will return a byte stream encoded in utf-8. If you need a stream in another encoding, take a look at a project like [iconv-lite ↗](https://www.npmjs.com/package/iconv-lite), which provides transform streams for transcoding text.
> 

---

### `renderToStaticNodeStream()` {#rendertostaticnodestream}

```jsx
ReactDOMServer.renderToStaticNodeStream(element)
```

Similar to [`renderToNodeStream`](/react/18.1/api-reference/react-dom-server#rendertonodestream), except this doesn’t create extra DOM attributes that React uses internally, such as `data-reactroot`. This is useful if you want to use React as a simple static page generator, as stripping away the extra attributes can save some bytes.

The HTML output by this stream is exactly equal to what [`ReactDOMServer.renderToStaticMarkup`](/react/18.1/api-reference/react-dom-server#rendertostaticmarkup) would return.

If you plan to use React on the client to make the markup interactive, do not use this method. Instead, use [`renderToNodeStream`](/react/18.1/api-reference/react-dom-server#rendertonodestream) on the server and [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on the client.

> Note:
> Server-only. This API is not available in the browser.
> The stream returned from this method will return a byte stream encoded in utf-8. If you need a stream in another encoding, take a look at a project like [iconv-lite ↗](https://www.npmjs.com/package/iconv-lite), which provides transform streams for transcoding text.
> 

---

### `renderToString()` {#rendertostring}

```jsx
ReactDOMServer.renderToString(element)
```

Render a React element to its initial HTML. React will return an HTML string. You can use this method to generate HTML on the server and send the markup down on the initial request for faster page loads and to allow search engines to crawl your pages for SEO purposes.

If you call [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on a node that already has this server-rendered markup, React will preserve it and only attach event handlers, allowing you to have a very performant first-load experience.

> Note
> This API has limited Suspense support and does not support streaming.
> On the server, it is recommended to use either [`renderToPipeableStream`](/react/18.1/api-reference/react-dom-server#rendertopipeablestream) (for Node.js) or [`renderToReadableStream`](/react/18.1/api-reference/react-dom-server#rendertoreadablestream) (for Web Streams) instead.
> 

---

### `renderToStaticMarkup()` {#rendertostaticmarkup}

```jsx
ReactDOMServer.renderToStaticMarkup(element)
```

Similar to [`renderToString`](/react/18.1/api-reference/react-dom-server#rendertostring), except this doesn’t create extra DOM attributes that React uses internally, such as `data-reactroot`. This is useful if you want to use React as a simple static page generator, as stripping away the extra attributes can save some bytes.

If you plan to use React on the client to make the markup interactive, do not use this method. Instead, use [`renderToString`](/react/18.1/api-reference/react-dom-server#rendertostring) on the server and [`ReactDOM.hydrateRoot()`](/react/18.1/api-reference/react-dom-client#hydrateroot) on the client.
