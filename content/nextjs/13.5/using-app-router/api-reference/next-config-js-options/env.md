---
linkTitle: "env"
title: "next.config.js Options: env | Next.js"
description: "Learn to add and access environment variables in your Next.js application at build time."
weight: 7
type: docs
---

# env

> Since the release of [Next.js 9.4 ↗](https://nextjs.org/blog/next-9-4) we now have a more intuitive and ergonomic experience for [adding environment variables](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables). Give it a try!
> 

> **Good to know**: environment variables specified in this way will **always** be included in the JavaScript bundle, prefixing the environment variable name with `NEXT_PUBLIC_` only has an effect when specifying them [through the environment or .env files](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables).
> 

To add environment variables to the JavaScript bundle, open `next.config.js` and add the `env` config:


next.config.js
```
module.exports = {
  env: {
    customKey: 'my-value',
  },
}
```

Now you can access `process.env.customKey` in your code. For example:

```
function Page() {
  return <h1>The value of customKey is: {process.env.customKey}</h1>
}
 
export default Page
```

Next.js will replace `process.env.customKey` with `'my-value'` at build time. Trying to destructure `process.env` variables won't work due to the nature of webpack [DefinePlugin ↗](https://webpack.js.org/plugins/define-plugin/).

For example, the following line:

```
return <h1>The value of customKey is: {process.env.customKey}</h1>
```

Will end up being:

```
return <h1>The value of customKey is: {'my-value'}</h1>
```
