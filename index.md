jus is a development server and build tool for making static websites with no configuration and no boilerplate code. It has built-in support for browserify, ES6, ES2015, React JSX, GitHub Flavored markdown, Sass, Less, Stylus, Myth, Handlebars and more.

## Why?

The year is 2016 and you're building a new website. At first you just create a single `index.html` file with some inline `<script>` and `<style>` tags. This works for a bit, but soon your code grows and you decide to extract the styles and scripts into standalone files. This is slightly better, but eventually you want to do something more sophisticated, like writing your stylesheets in Sass, or concatenating and minifying assets, or using npm dependencies with [browserify](https://github.com/substack/browserify-handbook). These conveniences are essential to building a website of any magnitude, but setting them up is tedious and time-consuming. It's at this point in the project that your attention turns from the creative to the mundane. Rather than building, you're now configuring.

In this day and age, most developers would turn to [Gulp](http://gulpjs.com/), [npm scripts](http://substack.net/task_automation_with_npm_run), [Jekyll](https://www.staticgen.com/jekyll) or one of [dozens of static site tools](https://www.staticgen.com). This is where `jus` comes in as an alternative.

There is no setup with jus. It has just two commands: `serve` and `build`. Run `jus serve` in your project directory and you've got a live develpment server running, watching for file changes, and serving your content with [clean URLs](#clean-urls). Write a `foo.sass` file and it'll be served at `/foo.css`. Use an npm-style `require` statement in your script, and jus will serve it up a browserified bundle. Write React's JSX syntax and it'll be transpiled to javascript on the fly. Write a GitHub-flavored `/markdown/file.md` and it'll be served as syntax-highlighted HTML at `/markdown/file`.

When it's time to deploy, run `jus build` to compile your site down into plain old HTML, CSS, and Javascript files, ready to deployed to [GitHub Pages](#deployment-to-github-pages) or [Surge](#deployment-to-surge).

## Getting Started

jus requires [node 4](https://nodejs.org/en/download/) or greater, because it uses some newer Javascript features. Install the command-line interface globally, then run it to see usage instructions:

```
npm i -g jus && jus
```

jus has a lot of dependencies, so it takes a while to install. Maybe go grab a :coffee: and read up
on [how to make npm faster](https://addyosmani.com/blog/using-npm-offline/).

If you like to learn by example, check out the repos of [sites using jus](#sites-using-jus). Otherwise, read on...

## Pages

Pages are written in Markdown, HTML, Handlebars, or any combination thereof. At render time each page is passed a handlebars context object containing metadata about all the files in the directory.

- Markdown parsing with [marky-markdown](npm.im/marky-markdown)
- GitHub-flavored Markdown support, including [fenced code blocks](https://help.github.com/articles/creating-and-highlighting-code-blocks/)
- Emoji support. Converts :emoji:-style shortcuts to unicode emojis.
- Syntax Highlighting with Atom.io's [highlights](npm.im/highlights)
- Supports [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/), including [fenced code blocks](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks)
- Extracts [HTML Frontmatter](https://www.npmjs.com/package/html-frontmatter) as metadata

Extensions: `html|hbs|handlebars|markdown|md`

## Scripts

All javascript files in your project are automatically [browserified](https://github.com/substack/browserify-handbook#readme) and [babelified](https://www.npmjs.com/package/babelify) using the `es2015` and `react` presets.

You can use node-style `require` statements to include npm modules in your code:

```js
const jQuery = require('jquery')

jQuery() => {
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

Scripts are browserified using [`babel-preset-react`](https://babeljs.io/docs/plugins/preset-react/), which means you
can write JSX in your scripts.

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

Delicious metadata is extracted from images and included in the handlebars context object, which is accessible to every page.

- Extracts [EXIF data](https://en.wikipedia.org/wiki/Exchangeable_image_file_format) from JPEGs, including [geolocation  data](https://en.wikipedia.org/wiki/Exchangeable_image_file_format#Geolocation).
- Extracts [dimensions](https://www.npmjs.com/package/image-size)
- Extracts [color palettes](https://www.npmjs.com/package/get-image-colors)

Extensions: `png|jpg|gif|svg`

## Datafiles

JSON and YML files are slurped into the handlebars context object, which is accessible to every page.

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

Now whenever you want to publish to GitHub Pages, run:

```sh
npm run deploy
```

Note: GitHub's [User Pages](https://help.github.com/articles/user-organization-and-project-pages/#user--organization-pages) (like `yourname.github.io`) are built from the `master` branch,
whereas [Project Pages](https://help.github.com/articles/user-organization-and-project-pages/#project-pages) (like `yourname.github.io/project`) are built from the `gh-pages` branch.
Be aware of this when setting up your npm scripts.

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

Now whenever you want to publish to Surge, run:

```sh
npm run deploy
```

## Prior Art

jus was inspired by a number of existing tools:

- [Harp](http://harpjs.com/): The main inspiration for jus. It was the first static site tool to introduce the concept of an [in-place asset pipeline](http://harpjs.com/docs/development/rules).
- [Jekyll](http://jekyllrb.com/): A blog-aware static site generator in Ruby. jus borrows the concept of frontmatter from Jekyll, but uses [HTML frontmatter](https://github.com/zeke/html-frontmatter#readme), unlike Jekyll's YAML frontmatter.
- [Brunch](http://brunch.io/#why): A lightweight tool for building HTML5 applications with emphasis on elegance and simplicity. The jus development server uses the [chokidar](https://www.npmjs.com/package/chokidar) module from Brunch to watch the filesystem.
- Ruby on Rails: The web development framework that helped popularize [Convention over Configuration](https://en.wikipedia.org/wiki/Convention_over_configuration)

## Sites using jus

Sometimes real examples are the easiest way to learn. Check out these open-source sites built with jus:

- [jus.js.org](https://github.com/zeke/jus.js.org), the site you're looking at now.
- [zeke.sikelianos.com](http://github.com/zeke/zeke.sikelianos.com), a personal portfolio site.
- [acrophony](https://github.com/zeke/acrophony#readme), an experimental React GUI for acrophonic alphabets.
