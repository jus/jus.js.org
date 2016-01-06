jus is a zero-configuration server and build tool for making static websites.
Write pages in Github-flavored Markdown, HTML, and Handlebars.
Use node-style `require` statements in your scripts and they'll be browserified automatically.
Write your Javascript in ES6, ES2015, or plain old ES5,
Write stylesheets in Stylus, Sass, SCSS, or plain old CSS.
Publish to GitHub Pages or surge.sh with ease.

<!-- Inspired by
tools like [Harp](http://harpjs.com/) and [Jekyll](http://jekyllrb.com/), and [Middleman](https://middlemanapp.com/). -->

## Getting Started

jus uses some fancy new Javascript language features, so [Node.js](https://nodejs.org/en/download/) version 4 or greater is required.

```sh
npm i jus --global
```

Once installed, run the server to watch a directory:

```
cd my/cool/website
jus serve
```

## Pages

Pages are written in Markdown, HTML, Handlebars, or any combination thereof. At render time each Page is passed a handlebars context object containing all the data about all the files in the directory.

- Markdown parsing with [marky-markdown](npm.im/marky-markdown)
- Syntax Highlighting with Atom.io's [highlights](npm.im/highlights)
- Supports [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/), including [fenced code blocks](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks)
- Extracts [HTML Frontmatter](https://www.npmjs.com/package/html-frontmatter) as metadata

Extensions: `html|hbs|handlebars|markdown|md`

## Scripts

Scripts can be written in ES5, ES6, and ES2015. They are [browserified](https://github.com/substack/browserify-handbook#readme) with [babelify](https://www.npmjs.com/package/babelify) using the `es2015` and `react` presets, which
means you can `require` or `import` node modules in them!

Extensions: `js|jsx|es|es6`

## Stylesheets

Stylesheets can be written in Sass, SCSS, Stylus, or plain CSS

Extensions: `css|sass|scss|styl`

## Templates

Templates are written in Handlebars.

- They must include a `{{{body}}}` string, to be used as a placeholder for where the main content should be rendered.
- They must have the word `layout` in their filename.
- If a file named `/layout.(html|hbs|handlebars|markdown|md)` is present, it will be applied to all pages by default.
- Pages can specify a custom layout in their [HTML frontmatter](https://www.npmjs.com/package/html-frontmatter). Specifying `layout: foo` will refer to the `/layout-foo.(html|hbs|handlebars|markdown|md)` layout file.
- Pages can disable layout by setting `layout: false`.

Extensions: `html|hbs|handlebars|markdown|md`

## Context

When the jus server is initialized, it recursively finds all the files in the directory tree,
ignoring `node_modules`, `.git`, and other unwanted patterns. These files are then stored in
memory in an array called `files`. For convenience, this list of files is broken down
into smaller arrays by type: an array for `pages`, another array for `scripts`, etc.

```
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


## Sites using jus

- [jus.js.org](https://github.com/zeke/jus.js.org) (this site!)
- [zeke.sikelianos.com](http://zeke.sikelianos.com), a personal portfolio site.
- [acrophony](https://github.com/zeke/acrophony#readme), an experimental React GUI for acrophonic alphabets.
