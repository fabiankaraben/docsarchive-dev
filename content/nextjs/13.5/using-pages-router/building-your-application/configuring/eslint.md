---
linkTitle: "ESLint"
title: "Configuring: ESLint | Next.js"
description: "Next.js reports ESLint errors and warnings during builds by default. Learn how to opt-out of this behavior here."
weight: 2
type: docs
---

# ESLint

Next.js provides an integrated [ESLint ↗](https://eslint.org/) experience out of the box. Add `next lint` as a script to `package.json`:


package.json
```
{
  "scripts": {
    "lint": "next lint"
  }
}
```

Then run `npm run lint` or `yarn lint`:


Terminal
```
yarn lint
```

If you don't already have ESLint configured in your application, you will be guided through the installation and configuration process.


Terminal
```
yarn lint
```

> You'll see a prompt like this:
> ? How would you like to configure ESLint?
> ❯ Strict (recommended)BaseCancel
> 

One of the following three options can be selected:

- **Strict**: Includes Next.js' base ESLint configuration along with a stricter [Core Web Vitals rule-set](/nextjs/13.5/using-pages-router/building-your-application/configuring/eslint#core-web-vitals). This is the recommended configuration for developers setting up ESLint for the first time.

  .eslintrc.json

  ```
  {
    "extends": "next/core-web-vitals"
  }
  ```

- **Base**: Includes Next.js' base ESLint configuration.

  .eslintrc.json

  ```
  {
    "extends": "next"
  }
  ```

- **Cancel**: Does not include any ESLint configuration. Only select this option if you plan on setting up your own custom ESLint configuration.

If either of the two configuration options are selected, Next.js will automatically install `eslint` and `eslint-config-next` as dependencies in your application and create an `.eslintrc.json` file in the root of your project that includes your selected configuration.

You can now run `next lint` every time you want to run ESLint to catch errors. Once ESLint has been set up, it will also automatically run during every build (`next build`). Errors will fail the build, while warnings will not.

> If you do not want ESLint to run during `next build`, refer to the documentation for [Ignoring ESLint](/nextjs/13.5/using-pages-router/api-reference/next-config-js-options/eslint).
> 

We recommend using an appropriate [integration ↗](https://eslint.org/docs/user-guide/integrations#editors) to view warnings and errors directly in your code editor during development.

## ESLint Config {#eslint-config}

The default configuration (`eslint-config-next`) includes everything you need to have an optimal out-of-the-box linting experience in Next.js. If you do not have ESLint already configured in your application, we recommend using `next lint` to set up ESLint along with this configuration.

> If you would like to use `eslint-config-next` along with other ESLint configurations, refer to the [Additional Configurations](/nextjs/13.5/using-pages-router/building-your-application/configuring/eslint#additional-configurations) section to learn how to do so without causing any conflicts.
> 

Recommended rule-sets from the following ESLint plugins are all used within `eslint-config-next`:

- [`eslint-plugin-react` ↗](https://www.npmjs.com/package/eslint-plugin-react)
- [`eslint-plugin-react-hooks` ↗](https://www.npmjs.com/package/eslint-plugin-react-hooks)
- [`eslint-plugin-next` ↗](https://www.npmjs.com/package/@next/eslint-plugin-next)

This will take precedence over the configuration from `next.config.js`.

## ESLint Plugin {#eslint-plugin}

Next.js provides an ESLint plugin, [`eslint-plugin-next` ↗](https://www.npmjs.com/package/@next/eslint-plugin-next), already bundled within the base configuration that makes it possible to catch common issues and problems in a Next.js application. The full set of rules is as follows:

 Enabled in the recommended configuration

||Rule|Description|
|---|---|---|
||[@next/next/google-font-display ↗](https://nextjs.org/docs/messages/google-font-display)|Enforce font-display behavior with Google Fonts.|
||[@next/next/google-font-preconnect ↗](https://nextjs.org/docs/messages/google-font-preconnect)|Ensure `preconnect` is used with Google Fonts.|
||[@next/next/inline-script-id ↗](https://nextjs.org/docs/messages/inline-script-id)|Enforce `id` attribute on `next/script` components with inline content.|
||[@next/next/next-script-for-ga ↗](https://nextjs.org/docs/messages/next-script-for-ga)|Prefer `next/script` component when using the inline script for Google Analytics.|
||[@next/next/no-assign-module-variable ↗](https://nextjs.org/docs/messages/no-assign-module-variable)|Prevent assignment to the `module` variable.|
||[@next/next/no-async-client-component ↗](https://nextjs.org/docs/messages/no-async-client-component)|Prevent client components from being async functions.|
||[@next/next/no-before-interactive-script-outside-document ↗](https://nextjs.org/docs/messages/no-before-interactive-script-outside-document)|Prevent usage of `next/script`'s `beforeInteractive` strategy outside of `pages/_document.js`.|
||[@next/next/no-css-tags ↗](https://nextjs.org/docs/messages/no-css-tags)|Prevent manual stylesheet tags.|
||[@next/next/no-document-import-in-page ↗](https://nextjs.org/docs/messages/no-document-import-in-page)|Prevent importing `next/document` outside of `pages/_document.js`.|
||[@next/next/no-duplicate-head ↗](https://nextjs.org/docs/messages/no-duplicate-head)|Prevent duplicate usage of `<Head>` in `pages/_document.js`.|
||[@next/next/no-head-element ↗](https://nextjs.org/docs/messages/no-head-element)|Prevent usage of `<head>` element.|
||[@next/next/no-head-import-in-document ↗](https://nextjs.org/docs/messages/no-head-import-in-document)|Prevent usage of `next/head` in `pages/_document.js`.|
||[@next/next/no-html-link-for-pages ↗](https://nextjs.org/docs/messages/no-html-link-for-pages)|Prevent usage of `<a>` elements to navigate to internal Next.js pages.|
||[@next/next/no-img-element ↗](https://nextjs.org/docs/messages/no-img-element)|Prevent usage of `<img>` element due to slower LCP and higher bandwidth.|
||[@next/next/no-page-custom-font ↗](https://nextjs.org/docs/messages/no-page-custom-font)|Prevent page-only custom fonts.|
||[@next/next/no-script-component-in-head ↗](https://nextjs.org/docs/messages/no-script-component-in-head)|Prevent usage of `next/script` in `next/head` component.|
||[@next/next/no-styled-jsx-in-document ↗](https://nextjs.org/docs/messages/no-styled-jsx-in-document)|Prevent usage of `styled-jsx` in `pages/_document.js`.|
||[@next/next/no-sync-scripts ↗](https://nextjs.org/docs/messages/no-sync-scripts)|Prevent synchronous scripts.|
||[@next/next/no-title-in-document-head ↗](https://nextjs.org/docs/messages/no-title-in-document-head)|Prevent usage of `<title>` with `Head` component from `next/document`.|
||@next/next/no-typos|Prevent common typos in [Next.js's data fetching functions ↗](https://nextjs.org/docs/pages/building-your-application/data-fetching.html)|
||[@next/next/no-unwanted-polyfillio ↗](https://nextjs.org/docs/messages/no-unwanted-polyfillio)|Prevent duplicate polyfills from Polyfill.io.|


If you already have ESLint configured in your application, we recommend extending from this plugin directly instead of including `eslint-config-next` unless a few conditions are met. Refer to the [Recommended Plugin Ruleset](/nextjs/13.5/using-pages-router/building-your-application/configuring/eslint#recommended-plugin-ruleset) to learn more.

### Custom Settings {#custom-settings}

#### `rootDir` {#rootdir}

If you're using `eslint-plugin-next` in a project where Next.js isn't installed in your root directory (such as a monorepo), you can tell `eslint-plugin-next` where to find your Next.js application using the `settings` property in your `.eslintrc`:


.eslintrc.json
```
{
  "extends": "next",
  "settings": {
    "next": {
      "rootDir": "packages/my-app/"
    }
  }
}
```

`rootDir` can be a path (relative or absolute), a glob (i.e. `"packages/*/"`), or an array of paths and/or globs.

## Linting Custom Directories and Files {#linting-custom-directories-and-files}

By default, Next.js will run ESLint for all files in the `pages/`, `app/`, `components/`, `lib/`, and `src/` directories. However, you can specify which directories using the `dirs` option in the `eslint` config in `next.config.js` for production builds:


next.config.js
```
module.exports = {
  eslint: {
    dirs: ['pages', 'utils'], // Only run ESLint on the 'pages' and 'utils' directories during production builds (next build)
  },
}
```

Similarly, the `--dir` and `--file` flags can be used for `next lint` to lint specific directories and files:


Terminal
```
next lint --dir pages --dir utils --file bar.js
```

## Caching {#caching}

To improve performance, information of files processed by ESLint are cached by default. This is stored in `.next/cache` or in your defined [build directory](/nextjs/13.5/using-pages-router/api-reference/next-config-js-options/distDir). If you include any ESLint rules that depend on more than the contents of a single source file and need to disable the cache, use the `--no-cache` flag with `next lint`.


Terminal
```
next lint --no-cache
```

## Disabling Rules {#disabling-rules}

If you would like to modify or disable any rules provided by the supported plugins (`react`, `react-hooks`, `next`), you can directly change them using the `rules` property in your `.eslintrc`:


.eslintrc.json
```
{
  "extends": "next",
  "rules": {
    "react/no-unescaped-entities": "off",
    "@next/next/no-page-custom-font": "off"
  }
}
```

### Core Web Vitals {#core-web-vitals}

The `next/core-web-vitals` rule set is enabled when `next lint` is run for the first time and the **strict** option is selected.


.eslintrc.json
```
{
  "extends": "next/core-web-vitals"
}
```

`next/core-web-vitals` updates `eslint-plugin-next` to error on a number of rules that are warnings by default if they affect [Core Web Vitals ↗](https://web.dev/vitals/).

> The `next/core-web-vitals` entry point is automatically included for new applications built with [Create Next App](/nextjs/13.5/using-app-router/api-reference/create-next-app).
> 

## Usage With Other Tools {#usage-with-other-tools}

### Prettier {#prettier}

ESLint also contains code formatting rules, which can conflict with your existing [Prettier ↗](https://prettier.io/) setup. We recommend including [eslint-config-prettier ↗](https://github.com/prettier/eslint-config-prettier) in your ESLint config to make ESLint and Prettier work together.

First, install the dependency:


Terminal
```
npm install --save-dev eslint-config-prettier
 
yarn add --dev eslint-config-prettier
 
pnpm add --save-dev eslint-config-prettier
 
bun add --dev eslint-config-prettier
```

Then, add `prettier` to your existing ESLint config:


.eslintrc.json
```
{
  "extends": ["next", "prettier"]
}
```

### lint-staged {#lint-staged}

If you would like to use `next lint` with [lint-staged ↗](https://github.com/okonet/lint-staged) to run the linter on staged git files, you'll have to add the following to the `.lintstagedrc.js` file in the root of your project in order to specify usage of the `--file` flag.


.lintstagedrc.js
```
const path = require('path')
 
const buildEslintCommand = (filenames) =>
  `next lint --fix --file ${filenames
    .map((f) => path.relative(process.cwd(), f))
    .join(' --file ')}`
 
module.exports = {
  '*.{js,jsx,ts,tsx}': [buildEslintCommand],
}
```

## Migrating Existing Config {#migrating-existing-config}

### Recommended Plugin Ruleset {#recommended-plugin-ruleset}

If you already have ESLint configured in your application and any of the following conditions are true:

- You have one or more of the following plugins already installed (either separately or through a different config such as `airbnb` or `react-app`):- `react`
  - `react-hooks`
  - `jsx-a11y`
  - `import`
- You've defined specific `parserOptions` that are different from how Babel is configured within Next.js (this is not recommended unless you have [customized your Babel configuration](/nextjs/13.5/using-pages-router/building-your-application/configuring/babel))
- You have `eslint-plugin-import` installed with Node.js and/or TypeScript [resolvers ↗](https://github.com/benmosher/eslint-plugin-import#resolvers) defined to handle imports

Then we recommend either removing these settings if you prefer how these properties have been configured within [`eslint-config-next` ↗](https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/index.js) or extending directly from the Next.js ESLint plugin instead:

```
module.exports = {
  extends: [
    //...
    'plugin:@next/next/recommended',
  ],
}
```

The plugin can be installed normally in your project without needing to run `next lint`:


Terminal
```
npm install --save-dev @next/eslint-plugin-next
 
yarn add --dev @next/eslint-plugin-next
 
pnpm add --save-dev @next/eslint-plugin-next
 
bun add --dev @next/eslint-plugin-next
```

This eliminates the risk of collisions or errors that can occur due to importing the same plugin or parser across multiple configurations.

### Additional Configurations {#additional-configurations}

If you already use a separate ESLint configuration and want to include `eslint-config-next`, ensure that it is extended last after other configurations. For example:


.eslintrc.json
```
{
  "extends": ["eslint:recommended", "next"]
}
```

The `next` configuration already handles setting default values for the `parser`, `plugins` and `settings` properties. There is no need to manually re-declare any of these properties unless you need a different configuration for your use case.

If you include any other shareable configurations, **you will need to make sure that these properties are not overwritten or modified**. Otherwise, we recommend removing any configurations that share behavior with the `next` configuration or extending directly from the Next.js ESLint plugin as mentioned above.