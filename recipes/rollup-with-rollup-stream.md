# Rollup with rollup-stream

Browserify と同様に、[Rollup](https://rollupjs.org/) はバンドラーであるため、パイプラインの先頭にある場合にのみ gulp に自然に適合します。Browserify と異なり、Roollup はストリームを出力としてネイティブに生成しないので、この位置を取る前にラップする必要があります。[rollup-stream](https://github.com/Permutatrix/rollup-stream) はこれを行い、Browserify の `bundle()` メソッドのような出力を生成します。その結果、Browserify レシピのほとんどは rollup-stream でも動作します。

## 基本的な使用法
```js
// npm install --save-dev gulp rollup-stream vinyl-source-stream
var gulp = require('gulp');
var rollup = require('rollup-stream');
var source = require('vinyl-source-stream');

gulp.task('rollup', function() {
  return rollup({
      entry: './src/main.js'
    })

    // give the file the name you want to output with
    .pipe(source('app.js'))

    // and output to ./dist/app.js as normal.
    .pipe(gulp.dest('./dist'));
});
```

## ソースマップとの使用法
```js
// npm install --save-dev gulp rollup-stream gulp-sourcemaps vinyl-source-stream vinyl-buffer
// optional: npm install --save-dev gulp-rename
var gulp = require('gulp');
var rollup = require('rollup-stream');
var sourcemaps = require('gulp-sourcemaps');
//var rename = require('gulp-rename');
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');

gulp.task('rollup', function() {
  return rollup({
      entry: './src/main.js',
      sourceMap: true
    })

    // point to the entry file.
    .pipe(source('main.js', './src'))

    // buffer the output. most gulp plugins, including gulp-sourcemaps, don't support streams.
    .pipe(buffer())

    // tell gulp-sourcemaps to load the inline sourcemap produced by rollup-stream.
    .pipe(sourcemaps.init({loadMaps: true}))

        // transform the code further here.

    // if you want to output with a different name from the input file, use gulp-rename here.
    //.pipe(rename('index.js'))

    // write the sourcemap alongside the output file.
    .pipe(sourcemaps.write('.'))

    // and output to ./dist/main.js as normal.
    .pipe(gulp.dest('./dist'));
});
```
