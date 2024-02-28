---
linkTitle: "Sass"
title: "Styling: Sass | Next.js"
description: "Style your Next.js application using Sass."
weight: 4
type: docs
---

# Sass

Next.js has built-in support for integrating with Sass after the package is installed using both the `.scss` and `.sass` extensions. You can use component-level Sass via CSS Modules and the `.module.scss`or `.module.sass` extension.

First, install [`sass` ↗](https://github.com/sass/sass):


Terminal
```
npm install --save-dev sass
```

> **Good to know**:
> Sass supports [two different syntaxes ↗](https://sass-lang.com/documentation/syntax), each with their own extension.
> The `.scss` extension requires you use the [SCSS syntax ↗](https://sass-lang.com/documentation/syntax#scss),
> while the `.sass` extension requires you use the [Indented Syntax ("Sass") ↗](https://sass-lang.com/documentation/syntax#the-indented-syntax).
> If you're not sure which to choose, start with the `.scss` extension which is a superset of CSS, and doesn't require you learn the
> Indented Syntax ("Sass").
> 

### Customizing Sass Options {#customizing-sass-options}

If you want to configure the Sass compiler, use `sassOptions` in `next.config.js`.


next.config.js
```
const path = require('path')
 
module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

### Sass Variables {#sass-variables}

Next.js supports Sass variables exported from CSS Module files.

For example, using the exported `primaryColor` Sass variable:


app/variables.module.scss
```
$primary-color: #64ff00;
 
:export {
  primaryColor: $primary-color;
}
```


app/page.js
```
// maps to root `/` URL
 
import variables from './variables.module.scss'
 
export default function Page() {
  return <h1 style={{ color: variables.primaryColor }}>Hello, Next.js!</h1>
}
```
