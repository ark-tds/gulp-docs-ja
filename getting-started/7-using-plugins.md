<!-- front-matter
id: using-plugins
title: Using Plugins
hide_title: true
sidebar_label: Using Plugins
-->

# プラグインの使用

gulp プラグインは、パイプラインでファイルを変換するための一般的な振る舞いをカプセル化する [Node Transform ストリーム][through2-docs]で、しばしば、`.pipe()` メソッドを用いて `src()` と `dest()` の間に置かれます。ストリームを通過するすべてのファイルのファイル名やメタデータ、内容を変化することができます。

npm のプラグインは、[プラグイン検索ページ][gulp-plugin-site]で "gulpplugin" と "gulpfriendly" キーワードを用いて閲覧、検索することができます。

各プラグインは小さな作業だけを行うので、ブロックを積み立てるようにそれらを接続することができます。望みの結果を得るためにそれらを束ねる必要があるかもしれません。

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');
const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    // The gulp-uglify plugin won't update the filename
    .pipe(uglify())
    // So use gulp-rename to change the extension
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

## プラグインが必要ですか？

gulp のすべてでプラグインを使用すべきではありません。それらは始めるのが手っ取り早い方法ですが、多くの操作はモジュールやライブラリを代わりに使用することで改善されています。

```js
const { rollup } = require('rollup');

// Rollup's promise API works great in an `async` task
exports.default = async function() {
  const bundle = await rollup({
    input: 'src/index.js'
  });

  return bundle.write({
    file: 'output/bundle.js',
    format: 'iife'
  });
}
```

プラグインは常にファイルを変換すべきです。その他の操作のためには、（プラグインではない）Node モジュールやライブラリを使用してください。

```js
const del = require('delete');

exports.default = function(cb) {
  // Use the `delete` module directly, instead of using gulp-rimraf
  del(['output/*.js'], cb);
}
```

## 条件的なプラグイン

プラグインの操作はファイルの種類を知るべきではないので、ファイルのサブセットを変換するためには [gulp-if][gulp-if-package] のようなプラグインが必要になるかもしれません。

```js
const { src, dest } = require('gulp');
const gulpif = require('gulp-if');
const uglify = require('gulp-uglify');

function isJavaScript(file) {
  // Check if file extension is '.js'
  return file.extname === '.js';
}

exports.default = function() {
  // Include JavaScript and CSS files in a single pipeline
  return src(['src/*.js', 'src/*.css'])
    // Only apply gulp-uglify plugin to JavaScript files
    .pipe(gulpif(isJavaScript, uglify()))
    .pipe(dest('output/'));
}
```

## インラインプラグイン

インラインプラグインは、望みの振る舞いを記述することによって gulpfile 内に定義する一時的な Transform ストリームです。

インラインプラグインを作成すると便利な状況が2つあります。
* プラグインを自作してメンテナンスする代わり
* 必要な機能を追加するために既存のプラグインをフォークする代わり

```js
const { src, dest } = require('gulp');
const uglify = require('uglify-js');
const through2 = require('through2');

exports.default = function() {
  return src('src/*.js')
    // Instead of using gulp-uglify, you can create an inline plugin
    .pipe(through2.obj(function(file, _, cb) {
      if (file.isBuffer()) {
        const code = uglify.minify(file.contents.toString())
        file.contents = Buffer.from(code.code)
      }
      cb(null, file);
    }))
    .pipe(dest('output/'));
}
```

[gulp-plugin-site]: https://gulpjs.com/plugins/
[through2-docs]: https://github.com/rvagg/through2
[gulp-if-package]: https://www.npmjs.com/package/gulp-if
