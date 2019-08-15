# シェルコマンドの実行

gulp から既存のコマンドラインツールを呼び出すと便利な場合もあります。

これを扱う2つの方法があります。node の [`child_process`](https://nodejs.org/api/child_process.html) 組み込みモジュール、またはコマンドを既存のパイプラインと統合する必要がある場合 [`gulp-exec`](https://github.com/robrich/gulp-exec) です。

```js
'use strict';

var cp = require('child_process');
var gulp = require('gulp');

gulp.task('reset', function() {
  // In gulp 4, you can return a child process to signal task completion
  return cp.execFile('git checkout -- .');
});
```

```js
'use strict';

var gulp = require('gulp');
var exec = require('gulp-exec');

gulp.task('reset', function() {
  return gulp.src('./**/**')
    .pipe(exec('git checkout -- <%= file.path %>'));
});
```
