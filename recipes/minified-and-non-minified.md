# 縮小版と非縮小版の両方の出力

組み合わせた JavaScript ファイルの縮小版と非縮小版の両方を出力するには、`gulp-rename` を使用して、`dest` へのパイピングを2回行います（縮小前に1回、縮小後に1回）。

```js
'use strict';

var gulp = require('gulp');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');

var DEST = 'build/';

gulp.task('default', function() {
  return gulp.src('foo.js')
    // This will output the non-minified version
    .pipe(gulp.dest(DEST))
    // This will minify and rename to foo.min.js
    .pipe(uglify())
    .pipe(rename({ extname: '.min.js' }))
    .pipe(gulp.dest(DEST));
});

```
