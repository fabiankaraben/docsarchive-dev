---
linkTitle: "TypeScript 1.1"
title: "TypeScript: Documentation - TypeScript 1.1"
description: "TypeScript 1.1 Release Notes"
weight: 40
type: docs
next: /typescript/5.2/declaration-files/introduction
---

# TypeScript 1.1

## Performance Improvements {#performance-improvements}

The 1.1 compiler is typically around 4x faster than any previous release. See [this blog post for some impressive charts. ↗](https://web.archive.org/web/20141007020020/http://blogs.msdn.com/b/typescript/archive/2014/10/06/announcing-typescript-1-1-ctp.aspx)

## Better Module Visibility Rules {#better-module-visibility-rules}

TypeScript now only strictly enforces the visibility of types in modules if the [`declaration` ↗](https://www.typescriptlang.org/tsconfig.html#declaration) flag is provided. This is very useful for Angular scenarios, for example:

```ts
module MyControllers {
interfaceZooScopeextendsng.IScope {
animals: Animal[];
  }
exportclassZooController {
// Used to be an error (cannot expose ZooScope), but now is only
// an error when trying to generate .d.ts files
constructor(public$scope: ZooScope) {}
/* more code */
  }
}
```
