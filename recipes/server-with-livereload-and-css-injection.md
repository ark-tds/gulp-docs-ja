# ライブリロードと CSS インジェクションを備えたサーバー

[BrowserSync](https://browsersync.io) と gulp を使用すると、同じ WiFi ネットワーク上の任意のデバイスにアクセスできる開発サーバーを簡単に作成できます。BrowserSync にはライブリロードも組み込まれているため、他に設定することはありません。

はじめに、モジュールをインストールします。

```sh
$ npm install --save-dev gulp browser-sync
```

そして、次のファイル構造を考えます。

```
gulpfile.js
app/
  styles/
    main.css
  scripts/
    main.js
  index.html
```

`gulpfile.js` 内で次のようにすると、`app` ディレクトリからファイルを簡単に提供し、それらに変更があるとすべてのブラウザーをリロードできます。

```js
var gulp = require('gulp');
var browserSync = require('browser-sync');
var reload = browserSync.reload;

// watch files for changes and reload
gulp.task('serve', function() {
  browserSync({
    server: {
      baseDir: 'app'
    }
  });

  gulp.watch(['*.html', 'styles/**/*.css', 'scripts/**/*.js'], {cwd: 'app'}, reload);
});

```

そして、`index.html` 内の CSS を含めます。

```html
<html>
  <head>
    ...
    <link rel="stylesheet" href="styles/main.css">
    ...

```

ファイルを提供し、デフォルト URL (http://localhost:3000) を指すブラウザウィンドウを起動するために、次を実行します。

```bash
gulp serve
```


## + CSS プリプロセス

一般的なユースケースは、CSS ファイルをプリプロセスした後にリロードすることです。例として Sass を使用すると、これはページ全体を更新せずに CSS をリロードするようブラウザに指示する方法です。

この更新されたファイル構造を考えます。

```
gulpfile.js
app/
  scss/
    main.scss
  scripts/
    main.js
  index.html
```

`gulpfile.js` 内で次のようにすると、`scss` ディレクトリから Sass ファイルを簡単に監視し、それらに変更があるとすべてのブラウザをリロードできます。

```js
var gulp = require('gulp');
var sass = require('gulp-ruby-sass');
var browserSync = require('browser-sync');
var reload = browserSync.reload;

gulp.task('sass', function() {
  return sass('scss/styles.scss')
    .pipe(gulp.dest('app/css'))
    .pipe(reload({ stream:true }));
});

// watch Sass files for changes, run the Sass preprocessor with the 'sass' task and reload
gulp.task('serve', gulp.series('sass', function() {
  browserSync({
    server: {
      baseDir: 'app'
    }
  });

  gulp.watch('scss/*.scss', gulp.series('sass'));
}));
```

そして、`index.html` 内にプリプロセスした CSS を含めます。

```html
<html>
  <head>
    ...
    <link rel="stylesheet" href="css/main.css">
    ...

```

ファイルを提供し、デフォルト URL (http://localhost:3000) を指すブラウザウィンドウを起動するために、次を実行します。

```bash
gulp serve
```

## 追加情報

- [BrowserStack](https://www.browserstack.com/) 仮想マシン内で、ライブリロード、CSS インジェクション、スクロール/フォーム同期がシームレスに動作します。
- ローカルサイトをパブリックな URL で見るためには `tunnel: true` を指定してください（すべての BrowserSync の機能を備えています）。
