
vdom-parser
===========

A client-side DOM to vdom parser based on DOMParser API, compatible with [virtual-dom](https://github.com/Matt-Esch/virtual-dom).

[![npm version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![coverage status][coveralls-image]][coveralls-url]

[![browser test report][sauce-labs-image]][sauce-labs-url]


# Motivation

We use `virtual-dom` with progressive enhancement in mind: we use server-side rendering to achieve good first-page performance then re-attach vdom rendering client-side.

This means we need a solid implementation of html to vdom parser, while there are [existing implementations](https://github.com/Matt-Esch/virtual-dom/wiki#html-to-vdom), we would like a solution that's well-tested and make use of existing browser API.

Hence `vdom-parser`, a small module that bridges the gap between server-side and client-side rendering.


# Features

- Use [DOMParser](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser) for better performance and smaller filesize (around 10KB when minified).
- No direct dependency, peer-dependent on `virtual-dom` major version (v2 currently).
- Key support to minimize re-rendering.
- 100% test coverage, covering common usage such as inline svg, style and script tags (unfortunately `phantomjs` still lack HTML support on DOMParser, so our coverage is only 99%).


# Browser support

- If your input is DOM element, then all modern browsers are supported.
- If your input is HTML string, then your browser need to [support HTML input on DOMParser API](http://caniuse.com/#search=DOMParser).
- By using [DOMParser polyfill for older browsers](https://github.com/bitinn/vdom-parser/blob/master/test/html-domparser.js) you can make this work on older browsers and phantomjs, but see test cases comment on potential gotcha. Always trim your string beforehand.
- If your input is HTML string and you need to support IE9, be aware of its limit. Using polyfill we can get most nodes under `document.body` to work, but anything else will throw `Invalid target element for this operation`. Maybe better to just [cut the mustard](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard) and use server-side rendering.
- If you need to support Opera 12 be aware that their DOM attributes are not namespaced, it doesn't affect `virtual-dom` diffing or patching, but don't expect `properties['xlink:href']` to exists.


# Install

`npm install vdom-parser --save`


# Usage

```javascript
// server-side render
var parser = require('vdom-parser');
var nodeCache = document.body;
var vdomCache = parser(nodeCache);

// client-side render
var vdom = h('div', 'hello');

// diff and patch
var patches = diff(vdomCache, vdom);
patch(nodeCache, patches);
```

See [test cases](https://github.com/bitinn/vdom-parser/blob/master/test/test.js) for more examples.


# API

## parser(node, key)

Returns a `VNode` or `VText`, see [virtual-dom documentation](https://github.com/Matt-Esch/virtual-dom/tree/master/docs).

### node

Should be a DOM Element or HTML String.

Note: for string input, `<html>`, `<body>`, `<head>` will return empty VText as we only support nodes under `document.body` or `document.head`. DOM element input doesn't have this limit.

### key

Optional attribute for [VNode key](https://github.com/Matt-Esch/virtual-dom/blob/master/docs/vnode.md) lookup, exposing VNode key as html attribute to minimize client-side patching.


# License

MIT


# Acknowledgement

Thanks to [marcelklehr/vdom-virtualize](https://github.com/marcelklehr/vdom-virtualize) and [TimBeyer/html-to-vdom](https://github.com/TimBeyer/html-to-vdom) for their work on this topic.


[npm-image]: https://img.shields.io/npm/v/vdom-parser.svg?style=flat-square
[npm-url]: https://www.npmjs.com/package/vdom-parser
[travis-image]: https://img.shields.io/travis/bitinn/vdom-parser.svg?style=flat-square
[travis-url]: https://travis-ci.org/bitinn/vdom-parser
[coveralls-image]: https://img.shields.io/coveralls/bitinn/vdom-parser.svg?style=flat-square
[coveralls-url]: https://coveralls.io/r/bitinn/vdom-parser

[sauce-labs-image]: https://saucelabs.com/browser-matrix/bitinn-vdom.svg
[sauce-labs-url]: https://saucelabs.com/u/bitinn-vdom
