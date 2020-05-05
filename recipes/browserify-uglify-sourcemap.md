# ソースマップをもつ Browserify + Uglify2

[Browserify](https://github.com/browserify/browserify) は重要かつ不可欠なツールになりましたが、
gulp でうまく動作させる前にラップする必要があります。以下は、オリジナルの個々のファイルへ解決する完全なソースマップをもつ
[Browserify](https://github.com/browserify/browserify) を使用するためのシンプルなレシピです。

ストリーム内で Browserify または ugilify を用いてエラーをハンドリングするためのレシピ [ストリームをハンドルエラーへ組み合わせる](https://github.com/gulpjs/gulp/blob/master/docs/recipes/combining-streams-to-handle-errors.md)も参照してください。

ソースマップをもつ Browserify + Uglify2 のためのシンプルな `gulpfile.js` ファイル：

``` javascript
'use strict';

var browserify = require('browserify');
var gulp = require('gulp');
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');
var uglify = require('gulp-uglify');
var sourcemaps = require('gulp-sourcemaps');
var log = require('gulplog');

gulp.task('javascript', function () {
  // set up the browserify instance on a task basis
  var b = browserify({
    entries: './entry.js',
    debug: true
  });

  return b.bundle()
    .pipe(source('app.js'))
    .pipe(buffer())
    .pipe(sourcemaps.init({loadMaps: true}))
        // Add transformation tasks to the pipeline here.
        .pipe(uglify())
        .on('error', log.error)
    .pipe(sourcemaps.write('./'))
    .pipe(gulp.dest('./dist/js/'));
});
```
