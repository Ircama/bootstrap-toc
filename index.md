---
layout: default
permalink: /
---

# Table of Contents plugin for Bootstrap
{: .page-header}

[![Build Status](https://travis-ci.org/afeld/bootstrap-toc.svg?branch=gh-pages)](https://travis-ci.org/afeld/bootstrap-toc)

This [Bootstrap](http://getbootstrap.com/) plugin allows you to generate a table of contents for any page, based on the heading elements (`<h1>`, `<h2>`, etc.). It is meant to emulate the sidebar you see on [the Bootstrap documentation site](http://getbootstrap.com/css/).

This page is an example of the plugin in action – the table of contents you see on the left (or top, on mobile) was automatically generated, without having to manually keep all of the navigation items in sync with the headings.

## Usage

On top of the normal Bootstrap setup (see their [Getting Started](http://getbootstrap.com/getting-started/) guide), you will need to include the Bootstrap Table of Contents stylesheet and JavaScript file.

```html
<!-- add after bootstrap.min.css -->
<link rel="stylesheet" href="https://cdn.rawgit.com/afeld/bootstrap-toc/v0.4.1/dist/bootstrap-toc.min.css">
<!-- add after bootstrap.min.js -->
<script src="https://cdn.rawgit.com/afeld/bootstrap-toc/v0.4.1/dist/bootstrap-toc.min.js"></script>
```

[Unminified versions](https://github.com/afeld/bootstrap-toc/tree/gh-pages/dist) are also available.

Next, pick one of the two options below.

### Via data attributes

*Simplest.*

Create a `<nav>` element with a `data-toggle="toc"` attribute.

```html
<nav id="toc" data-toggle="toc"></nav>
```

You can put this wherever on the page you like. Since this plugin leverages Bootstrap's [Scrollspy](http://getbootstrap.com/javascript/#scrollspy) plugin, you will also need to add a couple attributes to the `<body>`:

```html
<body data-spy="scroll" data-target="#toc">
```

### Via JavaScript

*If you need customization.*

If you prefer to create your navigation element another way (e.g. within single-page apps), you can pass a jQuery object into `Toc.init()`.

```html
<nav id="toc"></nav>
```

```javascript
$(function() {
  var navSelector = '#toc';
  var $myNav = $(navSelector);
  Toc.init($myNav);
  $('body').scrollspy({
    target: navSelector
  });
});
```

See the [Scrollspy](http://getbootstrap.com/javascript/#scrollspy) documentation for more information about initializing that plugin.

#### Options

When calling `Toc.init()`, you can either pass in the jQuery object for the `<nav>` element (as seen above), or an options object:

```javascript
Toc.init({
  $nav: $('#myNav'),
  // ...
});
```

All options are optional, unless otherwise indicated.

option | type | notes
--- | --- | ---
`$nav` | jQuery Object | (required) The element that the navigation will be created in.
`$scope` | jQuery Object | The element where the search for headings will be limited to, or the list of headings that will be used in the navigation. Defaults to `$(document.body)`.
{: .table }

## Customization

The following options can be specified at the heading level via `data-toc-*` attributes.

### Displayed text

By default, Bootstrap TOC will use the text from the heading element in the table of contents. If you want to customize what is displayed, add a `data-toc-text` attribute with the desired text. For example:

```html
<h2 data-toc-text="Short text">Longer text</h2>
```

displays "Longer text" as the heading, but "Short text" in the sidebar.

### Skipping

To prevent a particular heading from being added to the table of contents, add a `data-toc-skip` [boolean attribute](https://www.w3.org/TR/2008/WD-html5-20080610/semantics.html#boolean).

```html
<h2 data-toc-skip>Some heading you don't want in the nav</h2>
```

## Layout

This plugin isn't opinionated about where it should be placed on the page, but a common use case is to have the table of contents created as a "sticky" sidebar. We will leverage the [Affix](http://getbootstrap.com/javascript/#affix) plugin for this, and wrap the `<nav>` element in a `<div>` with a Bootstrap column class (see information about the [Grid](http://getbootstrap.com/css/#grid)). As an example putting it all together (similar to this page):

```html
<body data-spy="scroll" data-target="#toc">
  <div class="container">
    <div class="row">
      <!-- sidebar, which will move to the top on a small screen -->
      <div class="col-sm-3">
        <nav id="toc" data-spy="affix" data-toggle="toc"></nav>
      </div>
      <!-- main content area -->
      <div class="col-sm-9">
        ...
      </div>
    </div>
  </div>
</body>
```

You may also want to include this in your stylesheet:

```css
nav[data-toggle='toc'] {
  margin-top: 30px;
}

/* small screens */
@media (max-width: 768px) {
  /* override the Affix plugin so that the navigation isn't sticky */
  nav.affix[data-toggle='toc'] {
    position: static;
  }

  /* PICK ONE */
  /* don't expand nested items, which pushes down the rest of the page when navigating */
  nav[data-toggle='toc'] .nav .active .nav {
    display: none;
  }
  /* alternatively, if you *do* want the second-level navigation to be shown (as seen on this page on mobile), use this */
  nav[data-toggle='toc'] .nav .nav {
    display: block;
  }
}
```

## Additional options

By default, the plugin finds the first heading level (`<h1>`, then `<h2>`, etc.) that has more than one element and defaults to 1 (for `<h1>`). The identified level becomes the top heading and the plugin supports a single nesting level associated to it, including the subsequent heading only. E.g., if the identified top level is `<h1>`, the nested level will be `<h2>`, which is the next below it; if the top level is `<h2>`, the nested one will be `<h3>`. No additional level to the nested one and no previous level to the top one will be shown.

The plugin allows options to customize this behavior:

* `headings` (which can be a string): if set to a list of headers (in the form *hN,hM,...* where N, M, ... are levels, e.g., `h1,h2,h3`), all of them are searched instead of the first declared one and of its subsequent heading. Example: `headings: 'h1,h2,h3,h4,h5,h6'` (in this case, all of them are searched and not only `<h2>` and `<h3>` in case `<h2>` is the top level).
* `class`: (which can be a string): if set to a string, its value is used to customize the CSS class that the plugin sets for each description, concatenating it with the related header level. If not set, the default class heading will be `toc-nav-h`. Example: by default, a top level description (`<h1>`) has class`toc-nav-h1`, a second level description (`<h2>`) has class `toc-nav-h2`, etc. If the option `class` is set to e.g. `my-class`, a top level description (`<h1>`) will have class `my-class-h1`, a second level description (`<h2>`) will have class `my-class-h2`, etc. This feature allows adding CSS attributes to customize the descriptions according its header level.

Example of usage:

```javascript
Toc.init($myNav, {
  headings: 'h1,h2,h3,h4,h5,h6',
  class: 'my-class'
});
```

Example of CSS to customize the behavior of the plugin where the target documentation is supposed to mostly exploit `<h2>` as top level and `<h3>` as neighboring one (supposing to set `headings: 'h1,h2,h3,h4,h5,h6'` and not to change the default `class` option):

```css
/*
 * Customizations of Bootstrap Table of Contents v0.4.2
 */
nav[data-toggle='toc'] .nav .nav > li > a {
  padding-bottom: 3px !important;
}
.toc-nav-h1 {
  text-decoration: navy dotted underline;
  font-size: 15px !important;
  padding-left: 15px !important;
}
.toc-nav-h4 {
  font-size: 11px !important;
  padding-left: 40px !important;
}
.toc-nav-h5 {
  font-size: 11px !important;
  padding-left: 46px !important;
}
.toc-nav-h6 {
  font-size: 11px !important;
  padding-left: 52px !important;
}
```

The following example includes an additional setting for scrollspy (both `target` and `offset` options are used:

```html
<script>
try {
  $(function() {
    var navSelector = '#toc';
    var $myNav = $(navSelector);
    Toc.init($myNav, { headings: 'h1,h2,h3,h4,h5,h6' } );
    $('body').scrollspy({
      target: navSelector,
      offset: 220
    });
  });
}
catch(e) {
}
</script>
```

## See also

This plugin was heavily inspired by:

* [Bootstrap Docs Sidebar example](https://jsfiddle.net/gableroux/S2SMK/)
* [Tocify plugin](http://gregfranko.com/jquery.tocify.js/)
* [TOC plugin](http://projects.jga.me/toc/)

## Contributing

Questions, feature suggestions, and bug reports/fixes welcome!

### Manual testing

1. Run `bundle`.
1. Run `bundle exec jekyll serve`.
1. Open the various test templates:
    * [H2's](http://localhost:4000/bootstrap-toc/test/templates/h2s.html)
    * [Markdown](http://localhost:4000/bootstrap-toc/test/templates/markdown.html)
    * [No IDs](http://localhost:4000/bootstrap-toc/test/templates/no-ids.html)

### Automated testing

1. Run `npm install`.
1. Run `gulp test`/`gulp watch` (command-line), or `open test/index.html` (browser).

You can find the tests in [`test/toc-test.js`](test/toc-test.js).
