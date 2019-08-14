# エラーをハンドルするためにストリームを組み合わせる

デフォルトで、ストリーム上でエラーを発火すると、すでに `error` イベントに割り当てられたリスナーがない限り、エラーがスローされます。これは、ストリームの長いパイプラインを用いている場合、少し注意が必要です。

[stream-combiner2](https://github.com/substack/stream-combiner2) を使用することによって、一連のストリームを単一のストリームに変換できます。つまり、コードの1箇所で `error` イベントをリッスンするだけで済みます。

gulpfile で使用する例を示します。

```js
var combiner = require('stream-combiner2');
var uglify = require('gulp-uglify');
var gulp = require('gulp');

gulp.task('test', function() {
  return combiner.obj([
      gulp.src('bootstrap/js/*.js'),
      uglify(),
      gulp.dest('public/bootstrap')
    ])
    // any errors in the above streams will get caught
    // by this listener, instead of being thrown:
    .on('error', console.error.bind(console));
});
```
