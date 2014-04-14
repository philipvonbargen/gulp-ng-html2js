# lingon-ng-html2js [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Dependency Status][depstat-image]][depstat-url]

> A plugin for [Lingon](https://github.com/jpettersson/lingon) which generates AngularJS modules, which pre-load your HTML
code into the [$templateCache](http://docs.angularjs.org/api/ng.$templateCache). This way AngularJS doesn't need to
request the actual HTML files anymore.

## Usage

First, install `lingon-ng-html2js` as a development dependency:

```shell
npm install --save-dev lingon-ng-html2js
```

Then, add it to your `lingon.js`:

```javascript
var ngHtml2Js = require("lingon-ng-html2js");

lingon.preProcessor('html').add(function(params) {
  return ngHtml2js({
    moduleName: 'templates',
    base: 'source'
  });
});
```

The main reason to use this module would be optimization. By pre-loading the HTML files, you can spare requests and
loading time when the files are actually needed. When you are optimizing, you should do it properly. So, we should add
the following plugins: `gulp-htmlmin` and `gulp-uglify`:

```javascript
var ngHtml2js = require("lingon-ng-html2js");
var htmlmin = require("gulp-htmlmin");
var uglify = require("gulp-uglify");

lingon.preProcessor('html').add(function(params) {
  var processors = [];

  // only run minification for build task
  if (lingon.task == 'build') {
    processors.push(
      htmlmin({
        collapseWhitespace: true,
        removeComments: true
      })
    );
  }

  processors.push(
    ngHtml2js({
      moduleName: 'templates',
      base: 'source'
    })
  );

  return processors;
});

lingon.postProcessor('js').add(function(params) {
  // only run minification for build task
  if(lingon.task == 'build') {
    return uglify({
      outSourceMap: true
    });
  }
});
```

This way you end up with 1 single, minified Javascript file, which pre-loads all the (minified) HTML templates.

## API

### ngHtml2Js(options)

#### options.moduleName
Type: `String`

The name of the generated AngularJS module. Uses the file url if omitted.

#### options.prefix
Type: `String`

The prefix which should be prepended to the file path to generate the file url.

#### options.stripPrefix
Type: `String`

The prefix which should be subtracted from the file path to generate the file url.

#### options.base
Type: `String`

The base directory used for resolving the relative file path to generate the file url. Falls back to regular file.base if unset.


## License

[MIT License](http://en.wikipedia.org/wiki/MIT_License)

[npm-url]: https://npmjs.org/package/lingon-ng-html2js
[npm-image]: https://badge.fury.io/js/lingon-ng-html2js.png

[travis-url]: http://travis-ci.org/philipvonbargen/lingon-ng-html2js
[travis-image]: https://secure.travis-ci.org/philipvonbargen/lingon-ng-html2js.png?branch=master

[depstat-url]: https://david-dm.org/philipvonbargen/lingon-ng-html2js
[depstat-image]: https://david-dm.org/philipvonbargen/lingon-ng-html2js.png
