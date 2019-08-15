# Gulp 4 を用いた最小限の BrowserSync のセットアップ

[BrowserSync](https://www.browsersync.io/) は開発プロセスを合理化するための優れたツールで、
ライブリロードによってコードの変更を即座にブラウザに反映できます。
Gulp 4 を用いたライブリロードを行う BrowserSync サーバーのセットアップは非常に明瞭で簡単です。

## Step 1: 依存関係をインストールする

```
npm install --save-dev browser-sync
```

## Step 2: プロジェクト構造をセットアップする

```
src/
  scripts/
    |__ index.js
dist/
  scripts/
index.html
gulpfile.babel.js
```

ここでの目的は次のことができるようにすることです。
- `src/scripts/` 内のソーススクリプトファイルをビルドする。例えば babel でコンパイル、縮小化など。
- `index.html` で使用するために `dist/scripts` 内にコンパイルしたバージョンを置く
- ソースファイル内の変更を監視し、`dist` パッケージを再ビルドする
- `dist` パッケージの再ビルドごとに、変更を即座に反映するためにブラウザをリロードする

## Step 3: gulpfile を書く

gulpfile は3つの部分に分けられます。

### 1. 通常どおり dist パッケージを準備するタスクを書きます

詳細はメインの[README](https://github.com/gulpjs/gulp/blob/4.0/README.md#use-last-javascript-version-in-your-gulpfile)を参照してください。

```javascript
import babel from 'gulp-babel';
import concat from 'gulp-concat';
import del from 'del';
import gulp from 'gulp';
import uglify from 'gulp-uglify';

const paths = {
  scripts: {
    src: 'src/scripts/*.js',
    dest: 'dist/scripts/'
  }
};

const clean = () => del(['dist']);

function scripts() {
  return gulp.src(paths.scripts.src, { sourcemaps: true })
    .pipe(babel())
    .pipe(uglify())
    .pipe(concat('index.min.js'))
    .pipe(gulp.dest(paths.scripts.dest));
}
```

### 2. BrowserSync サーバーをセットアップする

そして、それに応じたサーバーの提供とリロードを行うタスクを書きます。

```javascript
import browserSync from 'browser-sync';
const server = browserSync.create();

function reload(done) {
  server.reload();
  done();
}

function serve(done) {
  server.init({
    server: {
      baseDir: './'
    }
  });
  done();
}
```

### 3. ソースの変更を監視し、スクリプトを再ビルドし、サーバーをリロードします

これは `gulp.series` で簡単に実現できます。

```javascript
const watch = () => gulp.watch(paths.scripts.src, gulp.series(scripts, reload));
```

## Step 4: すべて一緒にします

最後のステップはデフォルトタスクに公開して、

```javascript
const dev = gulp.series(clean, scripts, serve, watch);
export default dev;
```

実行します。

```bash
$ gulp
```

これで BrowserSync のデフォルトアドレス [http://localhost:3000](http://localhost:3000) にアクセスすると、
ソースファイルの内容を変更するたびにブラウザの最終結果が更新されることが分かります。
全体の gulpfile は次のとおりです。

```javascript
import babel from 'gulp-babel';
import concat from 'gulp-concat';
import del from 'del';
import gulp from 'gulp';
import uglify from 'gulp-uglify';
import browserSync from 'browser-sync';

const server = browserSync.create();

const paths = {
  scripts: {
    src: 'src/scripts/*.js',
    dest: 'dist/scripts/'
  }
};

const clean = () => del(['dist']);

function scripts() {
  return gulp.src(paths.scripts.src, { sourcemaps: true })
    .pipe(babel())
    .pipe(uglify())
    .pipe(concat('index.min.js'))
    .pipe(gulp.dest(paths.scripts.dest));
}

function reload(done) {
  server.reload();
  done();
}

function serve(done) {
  server.init({
    server: {
      baseDir: './'
    }
  });
  done();
}

const watch = () => gulp.watch(paths.scripts.src, gulp.series(scripts, reload));

const dev = gulp.series(clean, scripts, serve, watch);
export default dev;
```
