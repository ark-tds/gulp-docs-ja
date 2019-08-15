# ストリームファクトリーを用いたストリームの共有

複数のタスクで同じプラグインを使用する場合、DRY にするのに苦労するかもしれません。この方法を用いると、よく使用するストリームチェインを分割するためのファクトリーを作成することができます。

この作業を行うために [lazypipe](https://github.com/OverZealous/lazypipe) を使用します。

これはサンプルファイルです。

```js
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var coffee = require('gulp-coffee');
var jshint = require('gulp-jshint');
var stylish = require('jshint-stylish');

gulp.task('bootstrap', function() {
  return gulp.src('bootstrap/js/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter(stylish))
    .pipe(uglify())
    .pipe(gulp.dest('public/bootstrap'));
});

gulp.task('coffee', function() {
  return gulp.src('lib/js/*.coffee')
    .pipe(coffee())
    .pipe(jshint())
    .pipe(jshint.reporter(stylish))
    .pipe(uglify())
    .pipe(gulp.dest('public/js'));
});
```

そして、lazypipe を使用した後のファイルは次のようになります。

```js
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var coffee = require('gulp-coffee');
var jshint = require('gulp-jshint');
var stylish = require('jshint-stylish');
var lazypipe = require('lazypipe');

// give lazypipe
var jsTransform = lazypipe()
  .pipe(jshint)
  .pipe(jshint.reporter, stylish)
  .pipe(uglify);

gulp.task('bootstrap', function() {
  return gulp.src('bootstrap/js/*.js')
    .pipe(jsTransform())
    .pipe(gulp.dest('public/bootstrap'));
});

gulp.task('coffee', function() {
  return gulp.src('lib/js/*.coffee')
    .pipe(coffee())
    .pipe(jsTransform())
    .pipe(gulp.dest('public/js'));
});
```

複数のタスクで再利用されていた JavaScript パイプライン（JSHint + Uglify）をファクトリーに分割していることがわかります。これらのファクトリーは、必要なタスクで何度も再利用できます。また、ファクトリーをネストしたり、効果を高めるためにファクトリーをチェインすることができます。各共有パイプラインを分割すると、ワークフローを変更すると決定した場合に修正する箇所が、1つの中心的な箇所だけになります。
