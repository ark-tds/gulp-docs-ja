# gulp を用いた Mocha テストランナー

### すべてのテストに共有モジュールを渡す

```js
// npm install gulp gulp-mocha

var gulp = require('gulp');
var mocha = require('gulp-mocha');

gulp.task('default', function() {
  return gulp.src(['test/test-*.js'], { read: false })
    .pipe(mocha({
      reporter: 'spec',
      globals: {
        should: require('should')
      }
    }));
});
```

### ファイル変更時に mocha のテストを実行する

```js
// npm install gulp gulp-mocha gulplog

var gulp = require('gulp');
var mocha = require('gulp-mocha');
var log = require('gulplog');

gulp.task('mocha', function() {
    return gulp.src(['test/*.js'], { read: false })
        .pipe(mocha({ reporter: 'list' }))
        .on('error', log.error);
});

gulp.task('watch-mocha', function() {
    gulp.watch(['lib/**', 'test/**'], gulp.series('mocha'));
});
```
