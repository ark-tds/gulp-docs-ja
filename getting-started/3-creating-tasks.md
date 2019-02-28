<!-- front-matter
id: creating-tasks
title: Creating Tasks
hide_title: true
sidebar_label: Creating Tasks
-->

# タスクの作成

Due to some platform limitations, synchronous tasks aren't supported, though there is a pretty nifty [alternative].
各 gulp タスクは非同期的な JavaScript の関数で、エラーファーストコールバックを受け取り、ストリームやプロミンス、イベントエミッター、子プロセス、observable を返します（[詳細は後述][async-completion-docs]）。いくつかのプラットフォームの制限のため、同期的なタスクはサポートされていませんが、とてもすばらしい[代替][using-async-await-docs]があります。

## エクスポート

タスクは**パブリック**または**プライベート**とみなすことができます。

* **パブリックなタスク**は gulpfile からエクスポートされ、`gulp` コマンドによって実行できます。
* **プライベートなタスク**は内部的に使用されるように作られており、通常 `series()` または `parallel()` の構成の一部として使用されます。

プライベートなタスクは他のタスクと同じように見え、動作しますが、エンドユーザーはそれを独立して実行することはできません。タスクをパブリックに登録するためには、gulpfile からそれをエクスポートしてください。

```js
const { series } = require('gulp');

// The `clean` function is not exported so it can be considered a private task.
// It can still be used within the `series()` composition.
function clean(cb) {
  // body omitted
  cb();
}

// The `build` function is exported so it is public and can be run with the `gulp` command.
// It can also be used within the `series()` composition.
function build(cb) {
  // body omitted
  cb();
}

exports.build = build;
exports.default = series(clean, build);
```

![ALT TEXT MISSING][img-gulp-tasks-command]

<small>以前、`task()` は関数をタスクとして登録するために使用されていました。この API はいまだ利用可能ですが、エクスポートが機能しないエッジケースを除いて、エクスポートは主要な登録メカニズムであるべきです。</small>

## Compose tasks

Gulp provides two powerful composition methods, `series()` and `parallel()`, allowing individual tasks to be composed into larger operations. Both methods accept any number of task functions or composed operations.  `series()` and `parallel()` can be nested within themselves or each other to any depth.

To have your tasks execute in order, use the `series()` method.
```js
const { series } = require('gulp');

function transpile(cb) {
  // body omitted
  cb();
}

function bundle(cb) {
  // body omitted
  cb();
}

exports.build = series(transpile, bundle);
```

For tasks to run at maximum concurrency, combine them with the `parallel()` method.
```js
const { parallel } = require('gulp');

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.build = parallel(javascript, css);
```

Tasks are composed immediately when either `series()` or `parallel()` is called.  This allows variation in the composition instead of conditional behavior inside individual tasks.

```js
const { series } = require('gulp');

function minify(cb) {
  // body omitted
  cb();
}


function transpile(cb) {
  // body omitted
  cb();
}

function livereload(cb) {
  // body omitted
  cb();
}

if (process.env.NODE_ENV === 'production') {
  exports.build = series(transpile, minify);
} else {
  exports.build = series(transpile, livereload);
}
```

`series()` and `parallel()` can be nested to any arbitrary depth.

```js
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function cssTranspile(cb) {
  // body omitted
  cb();
}

function cssMinify(cb) {
  // body omitted
  cb();
}

function jsTranspile(cb) {
  // body omitted
  cb();
}

function jsBundle(cb) {
  // body omitted
  cb();
}

function jsMinify(cb) {
  // body omitted
  cb();
}

function publish(cb) {
  // body omitted
  cb();
}

exports.build = series(
  clean,
  parallel(
    cssTranspile,
    series(jsTranspile, jsBundle)
  ),
  parallel(cssMinify, jsMinify),
  publish
);
```

When a composed operation is run, each task will be executed every time it was referenced.  For example, a `clean` task referenced before two different tasks would be run twice and lead to undesired results.  Instead, refactor the `clean` task to be specified in the final composition.

If you have code like this:

```js
// This is INCORRECT
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

exports.build = parallel(css, javascript);
```

Migrate to this:

```js
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

exports.build = series(clean, parallel(css, javascript));
```

[async-completion-docs]: ../getting-started/4-async-completion.md
[using-async-await-docs]: ../getting-started/4-async-completion.md#using-async-await
[img-gulp-tasks-command]: https://gulpjs.com/img/docs-gulp-tasks-command.png
[async-once]: https://github.com/gulpjs/async-once
