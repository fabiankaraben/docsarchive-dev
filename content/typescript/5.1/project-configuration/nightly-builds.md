---
linkTitle: "Nightly Builds"
title: "TypeScript: Documentation - Nightly Builds"
description: "How to use a nightly build of TypeScript"
weight: 8
type: docs
---

# Nightly Builds

A nightly build from the [TypeScript’s `main`](https://github.com/Microsoft/TypeScript/tree/main) branch is published by midnight PST to npm.
Here is how you can get it and use it with your tools.

## Using npm {#using-npm}

```shell
npm install -g typescript@next
```

## Updating your IDE to use the nightly builds {#updating-your-ide-to-use-the-nightly-builds}

You can also update your IDE to use the nightly drop.
First you will need to install the package through npm.
You can either install the npm package globally or to a local `node_modules` folder.

The rest of this section assumes `typescript@next` is already installed.

### Visual Studio Code {#visual-studio-code}

Update `.vscode/settings.json` with the following:

```json
"typescript.tsdk": "<path to your folder>/node_modules/typescript/lib"
```

More information is available at [VSCode documentation](https://code.visualstudio.com/Docs/languages/typescript#_using-newer-typescript-versions).

### Sublime Text {#sublime-text}

Update the `Settings - User` file with the following:

```json
"typescript_tsdk": "<path to your folder>/node_modules/typescript/lib"
```

More information is available at the [TypeScript Plugin for Sublime Text installation documentation](https://github.com/Microsoft/TypeScript-Sublime-Plugin#installation).

### Visual Studio 2013 and 2015 {#visual-studio-2013-and-2015}

> Note: Most changes do not require you to install a new version of the VS TypeScript plugin.
> 

The nightly build currently does not include the full plugin setup, but we are working on publishing an installer on a nightly basis as well.

1. Download the [VSDevMode.ps1](https://github.com/Microsoft/TypeScript/blob/main/scripts/VSDevMode.ps1) script.
  > Also see our wiki page on [using a custom language service file](https://github.com/Microsoft/TypeScript/wiki/Dev-Mode-in-Visual-Studio#using-a-custom-language-service-file).
  > 
2. From a PowerShell command window, run:

For VS 2015:

```
VSDevMode.ps1 14 -tsScript <path to your folder>/node_modules/typescript/lib
```

For VS 2013:

```
VSDevMode.ps1 12 -tsScript <path to your folder>/node_modules/typescript/lib
```

### IntelliJ IDEA (Mac) {#intellij-idea-mac}

Go to `Preferences` > `Languages & Frameworks` > `TypeScript`:

> TypeScript Version: If you installed with npm: `/usr/local/lib/node_modules/typescript/lib`
> 

### IntelliJ IDEA (Windows) {#intellij-idea-windows}

Go to `File` > `Settings` > `Languages & Frameworks` > `TypeScript`:

> TypeScript Version: If you installed with npm: `C:\Users\USERNAME\AppData\Roaming\npm\node_modules\typescript\lib`
> 
