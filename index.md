jus is a development server and build tool for making static websites. Eliminate boilerplate code, avoid configuration, and focus on building.

- Write [content](/#pages) in Markdown and HTML.
- Write [scripts](/#scripts) in ES2015, ES6, and ES5.
- Write [stylesheets](/#stylesheets) in Sass, Less, Stylus, Myth, and CSS.
- Write [templates](/#templates) in Handlebars.
- [Publish](/#deployment) to GitHub Pages or Surge.

## Getting Started

jus requires [node 4](https://nodejs.org/en/download/) or greater, because it uses some newer Javascript features. Install the command-line interface globally, then run it to see usage instructions:

```
npm i -g jus && jus
```

## Pages

Pages are written in Markdown, HTML, Handlebars, or any combination thereof. At render time each Page is passed a handlebars context object containing all the data about all the files in the directory.

- Markdown parsing with [marky-markdown](npm.im/marky-markdown)
- Syntax Highlighting with Atom.io's [highlights](npm.im/highlights)
- Supports [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/), including [fenced code blocks](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks)
- Extracts [HTML Frontmatter](https://www.npmjs.com/package/html-frontmatter) as metadata

Extensions: `html|hbs|handlebars|markdown|md`

## Scripts

Scripts are automatically [browserified](https://github.com/substack/browserify-handbook#readme) and [babelified](https://www.npmjs.com/package/babelify) using the `es2015` and `react` presets.

Use node-style `require` statements to include npm modules in your code:

```js
const $ = require('jquery')

$() => {
  console.log(`welcome to ${location.hostname}`)
}
```

You can also use [ES6-style imports](http://babeljs.io/docs/learn-es2015/#modules), if you prefer:

```js
import React from 'react'
import ReactDOM from 'react-dom'
import domready from 'domready'

domready(() => {
  // do some React magic
})
```

Extensions: `js|jsx|es|es6`

## Stylesheets

Stylesheets can be written in
[Sass](http://sass-lang.com/),
[SCSS](http://sass-lang.com/),
[Less](http://lesscss.org/),
[Stylus](http://stylus-lang.com/),
[Myth](http://www.myth.io/),
or plain old CSS. Use whatever preprocessor suits your fancy.

Extensions: `css|less|sass|scss|styl`

## Templates

Templates are written in Handlebars.

- They must include a `{{{body}}}` string, to be used as a placeholder for where the main content should be rendered.
- They must have the word `layout` in their filename.
- If a file named `/layout.(html|hbs|handlebars|markdown|md)` is present, it will be applied to all pages by default.
- Pages can specify a custom layout in their [HTML frontmatter](https://www.npmjs.com/package/html-frontmatter). Specifying `layout: foo` will refer to the `/layout-foo.(html|hbs|handlebars|markdown|md)` layout file.
- Pages can disable layout by setting `layout: false`.

Extensions: `html|hbs|handlebars|markdown|md|mdown`

## Context

When the jus server is initialized, it recursively finds all the files in the directory tree,
ignoring `node_modules`, `.git`, and other unwanted patterns. These files are then stored in
memory in an array called `files`. For convenience, this list of files is broken down
into smaller arrays by type: an array for `pages`, another array for `scripts`, etc.

```js
{
  files: [...],
  pages: [...]
  scripts: [...]
  stylesheets: [...]
  images: [...]
  datafiles: [...]
}
```

When you request a page, the server renders the page on the fly, passing this object to the
given page's template. This means every page has access to metadata about
every file in the site at render time. You can use this to output to build a sitemap, for example:


Pages always have the following properties:

```js
{
  title: '',
  href: ''
}
```

Using handlebars in your pages is entirely optional. If your pages don't need to do any dynamic rendering at build time, that's okay. The context will simply be ignored at render time.

### Images

Delicious metadata is extracted from images and included in the handlebars context object, which is accessible to every Page.

- Extracts [EXIF data](https://en.wikipedia.org/wiki/Exchangeable_image_file_format) from JPEGs, including [geolocation  data](https://en.wikipedia.org/wiki/Exchangeable_image_file_format#Geolocation).
- Extracts [dimensions](https://www.npmjs.com/package/image-size)
- Extracts [color palettes](https://www.npmjs.com/package/get-image-colors)

Extensions: `png|jpg|gif|svg`

## Datafiles

JSON and YML files are slurped into the handlebars context object, which is accessible to every Page.

Extensions: `json|yaml|yml`

## Clean URLs

jus uses a clean URL strategy that is compatible with
[GitHub Pages](http://aseemk.github.io/gh-pages-test/)
and
[surge.sh](https://surge.sh/help/using-clean-urls-automatically).
In essence, [Pages](#pages) get their extension lopped off,
and pages named index inherit the name of their directory.

<table class="routes">
  <tr>
    <th>Filename</th>
    <th>URL</th>
  </tr>
  <tr>
    <td>index.html</td>
    <td>/</td>
  </td>
  <tr>
    <td>nested/index.html</td>
    <td>/nested</td>
  </td>
  <tr>
    <td>nested/page.html</td>
    <td>/nested/page</td>
  </td>
  <tr>
    <td>also/markdown.md</td>
    <td>/also/markdown</td>
  </td>
  <tr>
    <td>also/handlebars.hbs</td>
    <td>/also/handlebars</td>
  </td>
  <tr>
    <td>stylesheet.scss</td>
    <td>/stylesheet.css</td>
  </td>
  <tr>
    <td>stylesheet.sass</td>
    <td>/stylesheet.css</td>
  </td>
  <tr>
    <td>stylesheet.styl</td>
    <td>/stylesheet.css</td>
  </td>
  <tr>
    <td>stylesheet.styl</td>
    <td>/stylesheet.css</td>
  </td>
</table>

## Deployment to GitHub Pages

Add the following to your package.json:

```json
{
  "scripts": {
    "start": "jus serve",
    "deploy": "npm run build && npm run commit && npm run push && npm run open",
    "build": "jus build . dist",
    "commit": "git add dist && git commit -m 'update dist'",
    "push": "git subtree push --prefix dist origin gh-pages",
    "open": "open http://zeke.sikelianos.com"
  }
}
```

To build and deploy to GitHub Pages, run:

```sh
npm run deploy
```

Note: GitHub's [User Pages](https://help.github.com/articles/user-organization-and-project-pages/#user--organization-pages) (like `yourname.github.io`) are built from the `master` branch,
whereas [Project Pages](https://help.github.com/articles/user-organization-and-project-pages/#project-pages) (like `yourname.github.io/project`) are built from the `gh-pages` branch.

Note: GitHub's CDN can take a minute to update, so you might have to refresh a few times when visiting.

## Deployment to Surge

[surge.sh](https://surge.sh/) is an awesome new platform for publishing static websites.

Install the Surge CLI:

```sh
 npm i -g surge
 ```

Add the following to your package.json:

```json
{
  "scripts": {
    "start": "jus serve",
    "deploy": "npm run build && npm run build && npm run open",
    "build": "jus build . dist",
    "push": "surge dist YOUR-URL",
    "open": "open YOUR-URL"
  }
}
```

To build and deploy to Surge, run:

```sh
npm run deploy
```

## Prior Art

jus was inspired by a number of existing tools:

- [Harp](http://harpjs.com/): Static web server with built in preprocessing
- [Jekyll](http://jekyllrb.com/): A blog-aware static site generator in Ruby
- [Brunch](http://brunch.io/): A lightweight approach to building HTML5 applications with emphasis on elegance and simplicity
- Ruby on Rails: A web development framework that helped popularize [Convention over Configuration](https://en.wikipedia.org/wiki/Convention_over_configuration)

## Sites using jus

- [jus.js.org](https://github.com/zeke/jus.js.org) (this site!)
- [zeke.sikelianos.com](http://zeke.sikelianos.com), a personal portfolio site.
- [acrophony](https://github.com/zeke/acrophony#readme), an experimental React GUI for acrophonic alphabets.
