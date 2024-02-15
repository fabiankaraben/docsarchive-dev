---
linkTitle: "Introduction"
title: "TypeScript: Documentation - Introduction"
description: "How to write a high-quality TypeScript Declaration (d.ts) file"
weight: 1
type: docs
prev: /typescript/5.2/whats-new/typescript-1-1
---

# Introduction

The Declaration Files section is designed to teach you how to write a high-quality TypeScript Declaration File. We need to assume basic familiarity with the TypeScript language in order to get started.

If you haven’t already, you should read the [TypeScript Handbook](/typescript/5.2/handbook/basic-types)
to familiarize yourself with basic concepts, especially types and modules.

The most common case for learning how .d.ts files work is that you’re typing an npm package with no types.
In that case, you can jump straight to [Modules .d.ts](/typescript/5.2/declaration-files/d-ts-templates/module-d-ts).

The Declaration Files section is broken down into the following sections.

## [Declaration Reference](/typescript/5.2/declaration-files/by-example) {#declaration-reference}

We are often faced with writing a declaration file when we only have examples of the underlying library to guide us.
The [Declaration Reference](/typescript/5.2/declaration-files/by-example) section shows many common API patterns and how to write declarations for each of them.
This guide is aimed at the TypeScript novice who may not yet be familiar with every language construct in TypeScript.

## [Library Structures](/typescript/5.2/declaration-files/library-structures) {#library-structures}

The [Library Structures](/typescript/5.2/declaration-files/library-structures) guide helps you understand common library formats and how to write a proper declaration file for each format.
If you’re editing an existing file, you probably don’t need to read this section.
Authors of new declaration files are strongly encouraged to read this section to properly understand how the format of the library influences the writing of the declaration file.

In the Template section you’ll find a number of declaration files that serve as a useful starting point
when writing a new file. If you already know what your structure is, see the d.ts Template section in the sidebar.

## [Do’s and Don’ts](/typescript/5.2/declaration-files/do-s-and-don-ts) {#dos-and-donts}

Many common mistakes in declaration files can be easily avoided.
The [Do’s and Don’ts](/typescript/5.2/declaration-files/do-s-and-don-ts) section identifies common errors,
describes how to detect them,
and how to fix them.
Everyone should read this section to help themselves avoid common mistakes.

## [Deep Dive](/typescript/5.2/declaration-files/deep-dive) {#deep-dive}

For seasoned authors interested in the underlying mechanics of how declaration files work,
the [Deep Dive](/typescript/5.2/declaration-files/deep-dive) section explains many advanced concepts in declaration writing,
and shows how to leverage these concepts to create cleaner and more intuitive declaration files.

## [Publish to npm](/typescript/5.2/declaration-files/publishing) {#publish-to-npm}

The [Publishing](/typescript/5.2/declaration-files/publishing) section explains how to publish your declaration files to an npm package, and shows how to manage your dependent packages.

## [Find and Install Declaration Files](/typescript/5.2/declaration-files/consumption) {#find-and-install-declaration-files}

For JavaScript library users, the [Consumption](/typescript/5.2/declaration-files/consumption) section offers a few simple steps to locate and install corresponding declaration files.
