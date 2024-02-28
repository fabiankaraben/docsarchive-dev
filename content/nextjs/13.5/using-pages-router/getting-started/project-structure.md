---
linkTitle: "Project Structure"
title: "Getting Started: Project Structure | Next.js"
description: "A list of folders and files conventions in a Next.js project"
weight: 2
type: docs
---

# Next.js Project Structure

This page provides an overview of the file and folder structure of a Next.js project. It covers top-level files and folders, configuration files, and routing conventions within the `app` and `pages` directories.

## Top-level folders {#top-level-folders}

|||
|---|---|
|[`app` ↗](https://nextjs.org/docs/app/building-your-application/routing.html)|App Router|
|[`pages` ↗](https://nextjs.org/docs/pages/building-your-application/routing.html)|Pages Router|
|[`public`](/nextjs/13.5/using-app-router/building-your-application/optimizing/static-assets)|Static assets to be served|
|[`src` ↗](https://nextjs.org/docs/app/building-your-application/configuring/src-directory.html)|Optional application source folder|


## Top-level files {#top-level-files}

|||
|---|---|
|**Next.js**||
|[`next.config.js` ↗](https://nextjs.org/docs/app/api-reference/next-config-js.html)|Configuration file for Next.js|
|[`package.json`](/nextjs/13.5/using-app-router/getting-started/installation#manual-installation)|Project dependencies and scripts|
|[`instrumentation.ts`](/nextjs/13.5/using-app-router/building-your-application/optimizing/instrumentation)|OpenTelemetry and Instrumentation file|
|[`middleware.ts`](/nextjs/13.5/using-app-router/building-your-application/routing/middleware)|Next.js request middleware|
|[`.env`](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables)|Environment variables|
|[`.env.local`](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables)|Local environment variables|
|[`.env.production`](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables)|Production environment variables|
|[`.env.development`](/nextjs/13.5/using-app-router/building-your-application/configuring/environment-variables)|Development environment variables|
|[`.eslintrc.json`](/nextjs/13.5/using-app-router/building-your-application/configuring/eslint)|Configuration file for ESLint|
|`.gitignore`|Git files and folders to ignore|
|`next-env.d.ts`|TypeScript declaration file for Next.js|
|`tsconfig.json`|Configuration file for TypeScript|
|`jsconfig.json`|Configuration file for JavaScript|


## `app` Routing Conventions {#app-routing-conventions}

### Routing Files {#routing-files}

||||
|---|---|---|
|[`layout`](/nextjs/13.5/using-app-router/api-reference/file-conventions/layout)|`.js``.jsx``.tsx`|Layout|
|[`page` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/page.html)|`.js``.jsx``.tsx`|Page|
|[`loading`](/nextjs/13.5/using-app-router/api-reference/file-conventions/loading)|`.js``.jsx``.tsx`|Loading UI|
|[`not-found`](/nextjs/13.5/using-app-router/api-reference/file-conventions/not-found)|`.js``.jsx``.tsx`|Not found UI|
|[`error`](/nextjs/13.5/using-app-router/api-reference/file-conventions/error)|`.js``.jsx``.tsx`|Error UI|
|[`global-error`](/nextjs/13.5/using-app-router/api-reference/file-conventions/error#global-errorjs)|`.js``.jsx``.tsx`|Global error UI|
|[`route`](/nextjs/13.5/using-app-router/api-reference/file-conventions/route)|`.js``.ts`|API endpoint|
|[`template` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/template.html)|`.js``.jsx``.tsx`|Re-rendered layout|
|[`default` ↗](https://nextjs.org/docs/app/api-reference/file-conventions/default.html)|`.js``.jsx``.tsx`|Parallel route fallback page|


### Nested Routes {#nested-routes}

|||
|---|---|
|[`folder` ↗](https://nextjs.org/docs/app/building-your-application/routing.html#route-segments)|Route segment|
|[`folder/folder` ↗](https://nextjs.org/docs/app/building-your-application/routing.html#nested-routes)|Nested route segment|


### Dynamic Routes {#dynamic-routes}

|||
|---|---|
|[`[folder]` ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html#convention)|Dynamic route segment|
|[`[...folder]` ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html#catch-all-segments)|Catch-all route segment|
|[`[[...folder]]` ↗](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes.html#optional-catch-all-segments)|Optional catch-all route segment|


### Route Groups and Private Folders {#route-groups-and-private-folders}

|||
|---|---|
|[`(folder)`](/nextjs/13.5/using-app-router/building-your-application/routing/route-groups#convention)|Group routes without affecting routing|
|[`_folder` ↗](https://nextjs.org/docs/app/building-your-application/routing/colocation.html#private-folders)|Opt folder and all child segments out of routing|


### Parallel and Intercepted Routes {#parallel-and-intercepted-routes}

|||
|---|---|
|[`@folder`](/nextjs/13.5/using-app-router/building-your-application/routing/parallel-routes#convention)|Named slot|
|[`(.)folder` ↗](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes.html#convention)|Intercept same level|
|[`(..)folder` ↗](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes.html#convention)|Intercept one level above|
|[`(..)(..)folder` ↗](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes.html#convention)|Intercept two levels above|
|[`(...)folder` ↗](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes.html#convention)|Intercept from root|


### Metadata File Conventions {#metadata-file-conventions}

#### App Icons {#app-icons}

||||
|---|---|---|
|[`favicon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#favicon)|`.ico`|Favicon file|
|[`icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#icon)|`.ico``.jpg``.jpeg``.png``.svg`|App Icon file|
|[`icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#generate-icons-using-code-js-ts-tsx)|`.js``.ts``.tsx`|Generated App Icon|
|[`apple-icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#apple-icon)|`.jpg``.jpeg`, `.png`|Apple App Icon file|
|[`apple-icon`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/app-icons#generate-icons-using-code-js-ts-tsx)|`.js``.ts``.tsx`|Generated Apple App Icon|


#### Open Graph and Twitter Images {#open-graph-and-twitter-images}

||||
|---|---|---|
|[`opengraph-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#opengraph-image)|`.jpg``.jpeg``.png``.gif`|Open Graph image file|
|[`opengraph-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#generate-images-using-code-js-ts-tsx)|`.js``.ts``.tsx`|Generated Open Graph image|
|[`twitter-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#twitter-image)|`.jpg``.jpeg``.png``.gif`|Twitter image file|
|[`twitter-image`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/opengraph-image#generate-images-using-code-js-ts-tsx)|`.js``.ts``.tsx`|Generated Twitter image|


#### SEO {#seo}

||||
|---|---|---|
|[`sitemap`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/sitemap#static-sitemapxml)|`.xml`|Sitemap file|
|[`sitemap`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/sitemap#generate-a-sitemap)|`.js``.ts`|Generated Sitemap|
|[`robots`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/robots#static-robotstxt)|`.txt`|Robots file|
|[`robots`](/nextjs/13.5/using-app-router/api-reference/file-conventions/metadata-files/robots#generate-a-robots-file)|`.js``.ts`|Generated Robots file|


## `pages` Routing Conventions {#pages-routing-conventions}

### Special Files {#special-files}

||||
|---|---|---|
|[`_app`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-app)|`.js``.jsx``.tsx`|Custom App|
|[`_document`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-document)|`.js``.jsx``.tsx`|Custom Document|
|[`_error`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-error#more-advanced-error-page-customizing)|`.js``.jsx``.tsx`|Custom Error Page|
|[`404`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-error#404-page)|`.js``.jsx``.tsx`|404 Error Page|
|[`500`](/nextjs/13.5/using-pages-router/building-your-application/routing/custom-error#500-page)|`.js``.jsx``.tsx`|500 Error Page|


### Routes {#routes}

||||
|---|---|---|
|**Folder convention**|||
|[`index`](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts#index-routes)|`.js``.jsx``.tsx`|Home page|
|[`folder/index`](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts#index-routes)|`.js``.jsx``.tsx`|Nested page|
|**File convention**|||
|[`index`](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts#index-routes)|`.js``.jsx``.tsx`|Home page|
|[`file`](/nextjs/13.5/using-pages-router/building-your-application/routing/pages-and-layouts)|`.js``.jsx``.tsx`|Nested page|


### Dynamic Routes {#dynamic-routes-1}

||||
|---|---|---|
|**Folder convention**|||
|[`[folder]/index`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes)|`.js``.jsx``.tsx`|Dynamic route segment|
|[`[...folder]/index`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes#catch-all-segments)|`.js``.jsx``.tsx`|Catch-all route segment|
|[`[[...folder]]/index`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes#optional-catch-all-segments)|`.js``.jsx``.tsx`|Optional catch-all route segment|
|**File convention**|||
|[`[file]`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes)|`.js``.jsx``.tsx`|Dynamic route segment|
|[`[...file]`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes#catch-all-segments)|`.js``.jsx``.tsx`|Catch-all route segment|
|[`[[...file]]`](/nextjs/13.5/using-pages-router/building-your-application/routing/dynamic-routes#optional-catch-all-segments)|`.js``.jsx``.tsx`|Optional catch-all route segment|

