# 直列タスクの実行

デフォルトで、gulp CLI は最大限並行してタスクを実行します。例えば、すべてのタスクを一度に実行し、何も待ちません。タスクが特定の順序で実行される直列を作成する場合、`gulp.series` を使用すべきです。

```js
var gulp = require('gulp');
var doAsyncStuff = require('./stuff');

gulp.task('one', function(done) {
  doAsyncStuff(function(err){
      done(err);
  });
});

gulp.task('two', function(done) {
  // do things
  done();
});

gulp.task('default', gulp.series('one', 'two'));
```

依存パターンを使用したもうひとつの例。`clean` タスク操作を1回だけ実行するために [`async-once`](https://www.npmjs.com/package/async-once) を使用します。
```js
var gulp = require('gulp');
var del = require('del'); // rm -rf
var once = require('async-once');

gulp.task('clean', once(function(done) {
  // run only once.
  // for the next call to the clean task, once will call done with
  // the same arguments as the first call.
  del(['output'], done);
}));

gulp.task('templates', gulp.series('clean', function() {
  return gulp.src(['src/templates/*.hbs'])
    // do some concatenation, minification, etc.
    .pipe(gulp.dest('output/templates/'));
}));

gulp.task('styles', gulp.series('clean', function() {
  return gulp.src(['src/styles/app.less'])
    // do some hinting, minification, etc.
    .pipe(gulp.dest('output/css/app.css'));
}));

// templates and styles will be processed in parallel.
// `clean` will be guaranteed to complete before either start.
// `clean` operations will not be run twice,
// even though it is called as a dependency twice.
gulp.task('build', gulp.parallel('templates', 'styles'));

// an alias.
gulp.task('default', gulp.parallel('build'));
```

Gulp 4 のアンチパターンであり、ログに clean タスクが2回実行されていることが示されることに留意してください。代わりに、`templates` と `style` は専用の `clean:*` タスクを使用すべきです。
```js
var gulp = require('gulp');
var del = require('del');

gulp.task('clean:templates', function() {
  return del(['output/templates/']);
});

gulp.task('templates', gulp.series('clean:templates', function() {
  return gulp.src(['src/templates/*.hbs'])
    .pipe(gulp.dest('output/templates/'));
});

gulp.task('clean:styles', function() {
  return del(['output/css/']);
});

gulp.task('styles', gulp.series('clean:styles', function() {
  return gulp.src(['src/styles/app.less'])
    .pipe(gulp.dest('output/css/app.css'));
}));

gulp.task('build', gulp.parallel('templates', 'styles'));
gulp.task('default', gulp.parallel('build'));
```
