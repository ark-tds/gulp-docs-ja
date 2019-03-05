<!-- front-matter
id: working-with-files
title: Working with Files
hide_title: true
sidebar_label: Working with Files
-->

# ファイルの操作

`src()` と `dest()` メソッドはコンピュータ上のファイルと対話するために gulp によって公開されています。

`src()` はファイルシステムから読み込むための [glob][explaining-globs-docs] が与えられ、[Node stream][node-streams-docs] を生成します。一致するすべてのファイルを見つけ出し、ストリームを通過するためにそれらをメモリに読み込みます。

[タスクの生成][creating-tasks-docs] で述べたように、`src()` によって生成されたストリームは非同期完了を通知するために、タスクから返されるべきです。

```js
const { src, dest } = require('gulp');

exports.default = function() {
  return src('src/*.js')
    .pipe(dest('output/'));
}
```

ストリームの主要な API は Transform ストリームまたは Writable ストリームをチェインするための `.pipe()` メソッドです。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(dest('output/'));
}
```

`dest()` には出力ディレクトリの文字列が与えられ、一般的に終止ストリームとして使用される [Node stream][node-streams-docs] も生成します。パイプラインを通過したファイルを受け取ったとき、その内容と他の詳細を指定されたディレクトリのファイルシステムに書き出します。`symlink()` メソッドは `dest()` のように動作しますが、ファイルの代わりにリンクを作成します。（詳細は [`symlink()`][symlink-api-docs] を参照してください。）


ほとんどの場合、プラグインは `.pipe()` メソッドを用いながら `src()` と `dest()` の間に配置され、ストリーム内のファイルを変換します。

## ストリームへのファイルの追加

与えられた glob に基づいてファイルをストリームに追加するために `src()` をパイプラインの間に置くこともできます。追加したファイルはストリームの後半の変換でのみ使用可能です。[glob が重なっている場合][overlapping-globs-docs]、ファイルは再度追加されます。

これはプレインな Javascript ファイルをパイプラインに追加してすべてを醜くする前に、いくつかのファイルをトランスパイルするのに役立ちます。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(uglify())
    .pipe(dest('output/'));
}
```

## 段階的な出力

中間状態をファイルシステムに書き出すために `dest()` をパイプラインの間で使用することもできます。ファイルを受け取ったとき、現在の状態はファイルシステムに書き出され、出力ファイルの新しい場所を示すようにパスが更新されます。そして、このファイルはパイプラインの下降を続けます。

この特徴は、同じパイプラインで非縮小ファイルと縮小ファイルを生成するのに役立ちます。

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');
const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(dest('output/'))
    .pipe(uglify())
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

## モード:ストリーム、バッファ、空

`src()` はバッファ、ストリーミング、空の3つのモードで動作できます。これらは `src()` の `buffer` と `read` [オプション][src-options-api-docs]で設定できます。

* バッファモードはデフォルトで、ファイルの内容をメモリに読み込みます。プラグインは通常ストリーミングモードをサポートしていません。
* ストリーミングモードは巨大な画像や動画のようなメモリに収まらないような大きなファイルを操作するために主に存在します。内容は一度に読み込まれるのではなく、小さなまとまちでファイルシステムからストリーミングされます。ストリーミングモードが必要な場合、それをサポートするプラグインを探すか、あなた自身のものを書いてください。
* 空モードは内容が含まれておらず、ファイルのメタデータを扱う場合にのみ役立ちます。

[explaining-globs-docs]: ../getting-started/6-explaining-globs.md
[creating-tasks-docs]: ../getting-started/3-creating-tasks.md
[overlapping-globs-docs]: ../getting-started/6-explaining-globs.md#overlapping-globs
[node-streams-docs]: https://nodejs.org/api/stream.html
[symlink-api-docs]: ../api/symlink.md
[src-options-api-docs]: ../api/src.md#options
