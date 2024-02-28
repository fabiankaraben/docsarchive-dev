---
linkTitle: "pageExtensions"
title: "next.config.js Options: pageExtensions | Next.js"
description: "Extend the default page extensions used by Next.js when resolving pages in the Pages Router."
weight: 16
type: docs
---

# pageExtensions

You can extend the default Page extensions (`.tsx`, `.ts`, `.jsx`, `.js`) used by Next.js. Inside `next.config.js`, add the `pageExtensions` config:


next.config.js
```
module.exports = {
  pageExtensions: ['mdx', 'md', 'jsx', 'js', 'tsx', 'ts'],
}
```

Changing these values affects *all* Next.js pages, including the following:

- [`middleware.js`](/nextjs/13.5/using-pages-router/building-your-application/routing/middleware)
- [`instrumentation.js`](/nextjs/13.5/using-pages-router/building-your-application/optimizing/instrumentation)
- `pages/_document.js`
- `pages/_app.js`
- `pages/api/`

For example, if you reconfigure `.ts` page extensions to `.page.ts`, you would need to rename pages like `middleware.page.ts`, `instrumentation.page.ts`, `_app.page.ts`.

## Including non-page files in the `pages` directory {#including-non-page-files-in-the-pages-directory}

You can colocate test files or other files used by components in the `pages` directory. Inside `next.config.js`, add the `pageExtensions` config:


next.config.js
```
module.exports = {
  pageExtensions: ['page.tsx', 'page.ts', 'page.jsx', 'page.js'],
}
```

Then, rename your pages to have a file extension that includes `.page` (e.g. rename `MyPage.tsx` to `MyPage.page.tsx`). Ensure you rename *all* Next.js pages, including the files mentioned above.
