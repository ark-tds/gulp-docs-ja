# ファイルやフォルダを削除する

ビルドを実行する前にいくつかのファイルを削除したいと思うかもしれません。ファイルを削除してもファイルの内容に影響はないので、gulp プラグインを使用する理由はありません。バニラ node モジュールを使用する絶好の機会です。

この例では、複数のファイルと glob をサポートしている [`del`](https://github.com/sindresorhus/del) モジュールを使用しましょう。

```sh
$ npm install --save-dev gulp@next del
```

次のファイル構造を考えてください。

```
.
├── dist
│   ├── report.csv
│   ├── desktop
│   └── mobile
│       ├── app.js
│       ├── deploy.json
│       └── index.html
└── src
```

gulpfile で、ビルドを実行する前に `mobile` フォルダの中身を削除します。
 
```js
var gulp = require('gulp');
var del = require('del');

gulp.task('clean:mobile', function () {
  return del([
    'dist/report.csv',
    // here we use a globbing pattern to match everything inside the `mobile` folder
    'dist/mobile/**/*',
    // we don't want to clean this file though so we negate the pattern
    '!dist/mobile/deploy.json'
  ]);
});

gulp.task('default', gulp.series('clean:mobile'));
```


## パイプラインでファイルを削除する

パイプラインでいくつかのファイルを処理した後、それらを削除したいかもしれません。

ストリーム内のファイルのパスを簡単に取得し、`del` メソッドへ渡すために、[vinyl-paths](https://github.com/sindresorhus/vinyl-paths) を使用しましょう。

```sh
$ npm install --save-dev gulp@next del vinyl-paths
```

次のファイル構造を考えてください。

```
.
├── tmp
│   ├── rainbow.js
│   └── unicorn.js
└── dist
```

```js
var gulp = require('gulp');
var stripDebug = require('gulp-strip-debug'); // only as an example
var del = require('del');
var vinylPaths = require('vinyl-paths');

gulp.task('clean:tmp', function () {
  return gulp.src('tmp/*')
    .pipe(vinylPaths(del))
    .pipe(stripDebug())
    .pipe(gulp.dest('dist'));
});

gulp.task('default', gulp.series('clean:tmp'));
```

これは tmp ディレクトリのみを削除します。


パイプラインで他のプラグインをすでに使用している場合にのみ、これを行ってください。そうでなければ、`gulp.src` はコストがかかるので、単にモジュールを直接使用してください。
