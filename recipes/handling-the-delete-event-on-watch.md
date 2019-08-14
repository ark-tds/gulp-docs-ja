# Watch 上の削除イベントのハンドリング

`gulp.watch` から返されるウォッチャーで発火する `'unlink'` イベントをリッスンできます。
これはファイルが削除されたとき発火されるので、次のような方法で出力先ディレクトリからファイルを削除できます。

```js
'use strict';

var del = require('del');
var path = require('path');
var gulp = require('gulp');
var header = require('gulp-header');
var footer = require('gulp-footer');

gulp.task('scripts', function() {
  return gulp.src('src/**/*.js', {base: 'src'})
    .pipe(header('(function () {\r\n\t\'use strict\'\r\n'))
    .pipe(footer('\r\n})();'))
    .pipe(gulp.dest('build'));
});

gulp.task('watch', function () {
  var watcher = gulp.watch('src/**/*.js', ['scripts']);

  watcher.on('unlink', function (filepath) {
    var filePathFromSrc = path.relative(path.resolve('src'), filepath);
    // Concatenating the 'build' absolute path used by gulp.dest in the scripts task
    var destFilePath = path.resolve('build', filePathFromSrc);
    del.sync(destFilePath);
  });
});
```
