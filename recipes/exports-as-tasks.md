# タスクとしてエクスポートする

ES2015 モジュールシンタックスを用いることで、エクスポートをタスクとして使用できます。

```js
import gulp from 'gulp';
import babel from 'gulp-babel';

// named task
export function build() {
  return gulp.src('src/*.js')
    .pipe(babel())
    .pipe(gulp.dest('lib'));
}

// default task
export default function dev() {
  gulp.watch('src/*.js', ['build']);
}
```

これは gulp 3.x にバンドルされている gulp-cli バージョンで動作**しません**。最新の公開バージョンを使用しなければなりません。
