<!-- front-matter
id: creating-tasks
title: Creating Tasks
hide_title: true
sidebar_label: Creating Tasks
-->

# タスクの作成

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

## タスクを合成する

gulp は2つの強力な合成メソッドである `series()` と `parallel()` を提供し、個々のタスクをより大きな操作にまとめることができます。両方のメソッドともタスク関数や合成操作をいくつでも受け入れます。`series()` と `parallel()` はそれら自身や互いにどんな深さでも入れ子にすることができます。

タスクを順番に実行するためには、`series()` メソッドを使用してください。
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

タスクを最大限並行実行するために、それらを `parallel()` メソッドと組み合わせてください。
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

`series()` または `parallel()` のどちらかが呼ばれるとタスクはすぐに合成されます。これにより、個々のタスク内の条件的な振る舞いによらず、多様な合成を行うことができます。

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

`series()` と `parallel()` はどんな深さにでもネストすることができます。

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

合成操作が実行されるとき、各タスクは参照されるごとに実行されます。例えば、2つの異なるタスクの前に参照された `clean` タスクは2回実行され、望まぬ結果になります。代わりに、`clean` タスクを最後のコンポジションで指定するようにリファクタリングしてください。


このようなコードだったら、

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

これに移行します。

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
