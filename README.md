[![view on npm](http://img.shields.io/npm/v/jsdoc-to-markdown.svg)](https://www.npmjs.org/package/jsdoc-to-markdown)
[![npm module downloads per month](http://img.shields.io/npm/dm/jsdoc-to-markdown.svg)](https://www.npmjs.org/package/jsdoc-to-markdown)
[![Build Status](https://travis-ci.org/75lb/jsdoc-to-markdown.svg?branch=master)](https://travis-ci.org/75lb/jsdoc-to-markdown)
[![Dependency Status](https://david-dm.org/75lb/jsdoc-to-markdown.svg)](https://david-dm.org/75lb/jsdoc-to-markdown)
![Analytics](https://ga-beacon.appspot.com/UA-27725889-32/jsdoc-to-markdown/README.md?pixel)

# jsdoc-to-markdown
[jsdoc](http://usejsdoc.org) documented source code in, markdown API documentation out. 

Features:

* Insert the output into a README, or any arbitrary document.
* Customisable to a granular level. If the output doesn't suit you, override it. 
* Works on html input files (experimental)

For information about the generation templates, customising output etc. please read the [dmd](https://github.com/75lb/dmd) docs.

## Synopsis
write documented code:
```js
/**
a quite wonderful function
@param {object} - privacy gown
@param {object} - security
@returns {survival}
*/
function protection(cloak, dagger){}
```

run a command:
```
$ jsdoc2md example/src/protection.js`
```

get markdown docs!
```handlebars
<a name="protection"></a>
##protection(cloak, dagger) ⇒ <code>survival</code>
a quite wonderful function

| Param  | Type                | Description  |
| ------ | ------------------- | ------------ |
| cloak  | <code>object</code> | privacy gown |
| dagger | <code>object</code> | security     |
```

this command achieves the same result: 
```sh
$ jsdoc-parse example/function.js | dmd
```

## Examples
Some examples of projects with `jsdoc2md` documentation.

<table>
  <thead>
    <tr><th>Project</th><th>Notes</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="https://github.com/75lb/handbrake-js">handbrake-js</a></td>
      <td>
        <p>A module exposing two methods and an inner class. The API docs are inserted into <a href="https://github.com/75lb/handbrake-js/tree/master/jsdoc2md">this README template</a> by this command: </p>
        <p><code>$ jsdoc2md --template jsdoc2md/README.hbs lib/*.js</code></p>
      </td>
    </tr>
    <tr>
      <td><a href="https://github.com/75lb/array-tools">array-tools</a></td>
      <td>Very simple module exporting a collection of methods. Demonstrates use of the <code>@category</code> tag to group identifiers in the member-list.</td>
    </tr>
    <tr>
      <td><a href="https://github.com/75lb/file-set">file-set</a></td>
      <td>Simple module exporting a class.</td>
    </tr>
  </tbody>
</table>

* [command-line-args](https://github.com/75lb/command-line-args)  (exports a class)

## Compatible Platforms
Tested on Mac OSX, Linux, Windows 8.1 and Windows XP.

## Usage
Document your source code using [correct jsdoc syntax](http://usejsdoc.org), then run it through `jsdoc2md`.

### Command-line tool
Install `jsdoc2md` globally:
```sh
$ npm install -g jsdoc-to-markdown
```

Options:
```
$ jsdoc2md -h

  jsdoc-to-markdown
  Markdown API documentation generator, good for Github projects

  Usage
  $ jsdoc2md [<options>] <source_files>

  -v, --verbose                    More verbose error reporting
  -h, --help                       Print usage information
  -j, --json                       Output the parsed jsdoc data only
  --private                        Include identifiers marked @private in the output
  --stats                          Print a few stats about the doclets parsed
  --html                           Enable experimental parsing of .html files
  --src <array>                    A list of javascript source files or glob expressions
  -t, --template <string>          A custom handlebars template to insert the rendered documentation into
  --heading-depth <number>         root heading depth, defaults to 2 (`##`).
  -p, --plugin <array>             Use an installed package containing helper and/or partial overrides
  --helper <array>                 handlebars helper files to override or extend the default set
  --partial <array>                handlebars partial files to override or extend the default set
  --sort-by <string>               none | kind | scope | kind,scope | scope,kind | <field_name>. Defaults to 'none'.
  --example-code                   Display all examples as code block
  --example-code-gfm <string>      Wraps each @example in a GFM fenced-code block(if not already done
                                   so in the source).. specify a language, e.g. `--example-code-gfm js`
  --name-format                    Format identifier names as code
  --github                         Use github-specific markdown for improved rendering on @todo and @deprecated
  --separators                     Put <hr> breaks between identifiers. Improves readability on bulky docs.
  --module-index-format <string>   list, table, dl
  --global-index-format <string>   list, table, dl
  --param-list-format <string>     list, table
  --property-list-format <string>  list, table  
```

Some typical use cases: 

```sh
$ # dump everything you have into a single file
$ jsdoc2md src/**/*.js > api.md
```

```sh
$ # split into separate files
$ jsdoc2md src/main-module.js > main-module.md
$ jsdoc2md src/important-class.js > important-class.md
```

```sh
$ # embed documentation into a template you made
$ jsdoc2md src/**/*.js --template readme.hbs > README.md
```

### Bundled with your project
#### As an `npm run` task
```sh
$ npm install jsdoc-to-markdown --save-dev
```

Then add a `docs` build script to your `package.json`, e.g.:
```json
{
  "name": "my-web-app",
  "version": "1.0.0",
  "scripts": {
    "docs": "jsdoc2md lib/*.js > api.md"
  }
}
```
Docs are generated like so:

```sh
$ npm run docs
```

#### As a grunt plug-in
See [grunt-jsdoc-to-markdown](https://github.com/75lb/grunt-jsdoc-to-markdown).

#### As a gulp task
Currently, the most reliable and natural way of using jsdoc2md with gulp. If your source code contains `@module` tags, use this method *only* ([reason](https://github.com/75lb/gulp-jsdoc-to-markdown#warning)). You should only need to edit `src`, `dest` and `options`: 

```js
var jsdoc2md = require("jsdoc-to-markdown");
var gutil = require("gulp-util");
var fs = require("fs");

gulp.task("docs", function(done){
    var src = "lib/**/*.js";
    var dest = "api.md";
    var options = {};
    
    gutil.log("writing documentation to " + dest);
    jsdoc2md.render(src, options)
        .on("error", function(err){
            gutil.log(gutil.colors.red("jsdoc2md failed"), err.message);
        })
        .pipe(fs.createWriteStream(dest));
});
```

#### As a gulp plug-in
See [gulp-jsdoc-to-markdown](https://github.com/75lb/gulp-jsdoc-to-markdown).

## Composition
Essentially, it connects the output of [jsdoc-parse](https://github.com/75lb/jsdoc-parse) to the input of [dmd](https://github.com/75lb/dmd).  

## API Reference
**Todo**

- [ ] Internationalisation

**Example**  
```js
var jsdoc2md = require("jsdoc-to-markdown");
```
<a name="module_jsdoc-to-markdown.render"></a>
### jsdoc2md.render(src, options) ⇒ <code>stream</code>
Transforms jsdoc into markdown documentation.

**Kind**: static method of <code>[jsdoc-to-markdown](#module_jsdoc-to-markdown)</code>  

| Param | Type | Description |
| --- | --- | --- |
| src | <code>string</code> \| <code>Array.&lt;string&gt;</code> | The javascript source file(s). |
| options | <code>object</code> | The render options |
| [options.template] | <code>string</code> | A custom handlebars template to insert the rendered documentation into. |
| [options.json] | <code>boolean</code> | Output the parsed jsdoc data only |
| [options.private] | <code>boolean</code> | Include symbols marked @private in the output |
| [options.stats] | <code>boolean</code> | Print a few stats about the doclets parsed |
| [options.heading-depth] | <code>number</code> | root heading depth, defaults to 1 (`#`) |
| [options.plugin] | <code>string</code> \| <code>Array.&lt;string&gt;</code> | Use an installed package containing helper and/or partial overrides |
| [options.helper] | <code>string</code> \| <code>Array.&lt;string&gt;</code> | handlebars helper files to override or extend the default set |
| [options.partial] | <code>string</code> \| <code>Array.&lt;string&gt;</code> | handlebars partial files to override or extend the default set |

**Example**  
Two ways to use `render`. Either pass in filepaths (`**` glob matching supported) of javascript source files:
```js
> jsdoc2md.render("lib/*.js").pipe(process.stdout);
```
or pipe in source code from another source:
```js
> fs.createReadStream("lib/main.js").pipe(jsdoc2md.render()).pipe(process.stdout);
```
&copy; 2015 Lloyd Brookes <75pound@gmail.com>
