# [gulp](https://github.com/gulpjs/gulp)-watch [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Coverage Status][coveralls-image]][coveralls-url] [![Dependency Status][depstat-image]][depstat-url]
> Watch, that actually is an endless stream

This is an implementation of [`gulp.watch`](https://github.com/gulpjs/gulp/blob/master/docs/API.md#gulpwatchglob—opts-cb) with an endless-stream approach. If `gulp.watch` is working for you, stick with it; otherwise, you can try this `gulp-watch` plugin.

The main reason for `gulp-watch`'s existence is that it can easily achieve per-file rebuilding on file change:

![Awesome demonstration](https://github.com/floatdrop/gulp-watch/raw/master/img/2014-01-09.gif)

## Usage

```js
var gulp = require('gulp'),
    concat = require('gulp-concat');

gulp.task('default', function () {
    watch('css/**/*.css', function(files) {
        return files.pipe(concat('index.css'))
            .pipe(gulp.dest('./dist/'));
    }));
});
```

Or as continious stream:

```js
// Run before: `npm install gulp gulp-watch gulp-sass`

var gulp = require('gulp'),
    watch = require('gulp-watch'),
    concat = require('gulp-concat');

gulp.task('default', function () {
    watch('css/**/*.css')
        .pipe(concat('index.css'))
        .pipe(gulp.dest('./dist/'));
});
```

> __Protip:__ until gulpjs 4.0 is released, you can use [gulp-plumber](https://github.com/floatdrop/gulp-plumber) to prevent stops on errors.

## API

### watch(glob, [options, callback])

Creates watcher that will spy on files that were matched by `glob`.

This function has two behaviors modes based on presence of `callback` argument:

1. `undefined` — stream of [vinyl](https://github.com/wearefractal/vinyl) objects with additional `event` property.
2. `Function` — callback with batched events (see below).

#### Callback `function(events, [done])`

Events are grouped with [`gulp-batch`](https://github.com/floatdrop/gulp-batch).

 * `events` — is `Stream` of incoming events. Events will be grouped by timeout to prevent multiple tasks to be executed repeatedly by commands like `git pull`.
 * `done` — is callback for your function signal to batch once you are done. This allows you to run your callback as soon as the previous `end`.

#### Options

This object is passed to [`gaze` options](https://github.com/shama/gaze#properties) directly (refer to [`gaze` documentation](https://github.com/shama/gaze)). For __batched__ mode, we are using [`gulp-batch`](https://github.com/floatdrop/gulp-batch#api), so options from there are also available. And of course options for [`gulp.src`](https://github.com/gulpjs/gulp#gulpsrcglobs-options) are used too. If you do not want content from `watch`, then add `read: false` to the `options` object.

#### options.base
Type: `String`  
Default: `undefined`

Use explicit base path for files from `glob`.

#### options.name
Type: `String`  
Default: `undefined`

Name of the watcher. If it present in options, you will get more readable output:

![Naming watchers](https://github.com/floatdrop/gulp-watch/raw/master/img/naming.png)

#### options.verbose
Type: `Boolean`  
Default: `false`

This options will enable more verbose output (useful for debugging).

#### options.silent
Type: `Boolean`  
Default: `false`

This options will disable all output (useful for tests).

### Methods

Returned `Stream` from constructor have some useful methods:

 * `close()` — calling `gaze.close` and emitting `end`, after `gaze.close` is done.

### Events

 * `end` — all files are stop being watched.
 * `ready` — just re-emitted event from `gaze`.
 * `error` — when something happened inside callback, you will get notified.

# License

MIT (c) 2014 Vsevolod Strukchinsky (floatdrop@gmail.com)

[npm-url]: https://npmjs.org/package/gulp-watch
[npm-image]: http://img.shields.io/npm/v/gulp-watch.svg?style=flat

[travis-url]: https://travis-ci.org/floatdrop/gulp-watch
[travis-image]: http://img.shields.io/travis/floatdrop/gulp-watch.svg?style=flat

[coveralls-url]: https://coveralls.io/r/floatdrop/gulp-watch
[coveralls-image]: http://img.shields.io/coveralls/floatdrop/gulp-watch.svg?style=flat

[depstat-url]: https://david-dm.org/floatdrop/gulp-watch
[depstat-image]: http://img.shields.io/david/floatdrop/gulp-watch.svg?style=flat
