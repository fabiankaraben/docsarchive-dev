---
linkTitle: "Module: Plugin"
title: "TypeScript: Documentation - Module: Plugin"
description: ""
weight: 2
type: docs
canonical: /typescript/5.2/declaration-files/d-ts-templates/module-plugin-d-ts
---

# Module: Plugin

## The Impact of ES6 on Module Plugins {#the-impact-of-es6-on-module-plugins}

Some plugins add or modify top-level exports on existing modules.
While this is legal in CommonJS and other loaders, ES6 modules are considered immutable and this pattern will not be possible.
Because TypeScript is loader-agnostic, there is no compile-time enforcement of this policy, but developers intending to transition to an ES6 module loader should be aware of this.
