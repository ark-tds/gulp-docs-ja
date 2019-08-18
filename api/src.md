<!-- front-matter
id: src
title: src()
hide_title: true
sidebar_label: src()
-->

# src()

ファイルシステムから [Vinyl][vinyl-concepts] オブジェクトを読み込むためのストリームを作成する。

**注意：** BOM（byte order marks）は、UTF-8では目的がなく、`removeBOM` オプションを使用して無効にしない限り、`src()` によって読み込まれた UTF-8 ファイルから削除されます。

## 使用法

```javascript
const { src, dest } = require('gulp');

function copy() {
  return src('input/*.js')
    .pipe(dest('output/'));
}

exports.copy = copy;
```


## シグネチャ

```js
src(globs, [options])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| globs | string<br>array | ファイルシステム上を監視するための [Globs][globs-concepts]。 |
| options | object | 詳細は以下の[オプション][options-section]にて説明します。 |

### 返り値

与えられた glob に基づくファイルを追加するために、パイプラインの最初または途中で使用できるストリーム。

### エラー

`globs` 引数が1つのファイル（`foo/bar.js` など）にしか照合できず、一致するものが見つからない場合、"File not found with singular glob" というメッセージとともにエラーが投げられます。このエラーを抑制するためには、`allowEmpty` オプションを `true` に設定してください。

無効な glob が `globs` 内で与えられた場合、"Invalid glob argument" というメッセージとともにエラーが投げられます。

### オプション

**関数を受け入れるオプションの場合、渡された関数は各 Vinyl オブジェクトで呼び出され、リストされている別の型の値を返さなければなりません。**


| 名前 | 型 | 初期値 | 備考 |
|:--------:|:------:|------------|--------|
| buffer | boolean<br>function | true | true の場合、ファイルの内容はメモリにバッファされます。false の場合、Vinyl オブジェクトの `contents` プロパティは停止したストリームになります。大きなファイルの内容をバッファできない場合があります。<br>**注意：** プラグインは、コンテンツをストリーミングするためのサポートを実装しない場合があります。 |
| read | boolean<br>function | true | false の場合、ファイルは読み込まれず、それらの Vinyl オブジェクトは `.dest()` を介してディスクに書き込みできません。 |
| since | date<br>timestamp<br>function | | 設定した場合、指定した時間以降に変更されたファイルに対してのみ Vinyl オブジェクトが作成されます。 |
| removeBOM | boolean<br>function | true | true の場合、UTF-8 エンコードされたファイルから BOM を削除します。false の場合、BOM を無視します。 |
| sourcemaps | boolean<br>function | false | true の場合、作成された Vinyl オブジェクトで[ソースマップ][sourcemaps-section]を有効にします。インラインソースマップを読み込み、外部ソースマップリンクを解決します。 |
| resolveSymlinks | boolean<br>function | true | true の場合、ターゲットへのシンボリックリンクを再帰的に解決します。false の場合、シンボリックリンクを保持し、Vinyl オブジェクトの `symlink` プロパティを元のファイルパスに設定します。 |
| cwd | string | `process.cwd()` | 絶対パスを形成するために相対パスと結合されるディレクトリ。絶対パスでは無視されます。`globs` と `path.join()` の組み合わせを避けるために使用してください。<br>_このオプションは [glob-stream][glob-stream-external] へ直接渡されます。_ |
| base | string | | 作成された Vinyl オブジェクトで `base` プロパティを明示的に設定します。詳細は [API コンセプト][glob-base-concepts]にあります。<br>_このオプションは [glob-stream][glob-stream-external] へ直接渡されます。_ |
| cwdbase | boolean | false | true の場合、`cwd` と `base` オプションを揃えるべきです。<br>_このオプションは [glob-stream][glob-stream-external] へ直接渡されます。_ |
| root | string | | `globs` が解決されるルートパス。<br>_このオプションは [glob-stream][glob-stream-external] へ直接渡されます。_ |
| allowEmpty | boolean | false | false の場合、1つのファイル（`foo/bar.js` など）にしか照合できない `globs` が一致するものを見つけられなかった場合、エラーを投げます。true の場合、glob エラーを抑制します。<br>_このオプションは [glob-stream][glob-stream-external] へ直接渡されます。_ |
| uniqueBy | string<br>function | `'path'` | 文字列のプロパティ名または関数の結果を比較して、ストリームから重複を削除します。<br>**注意：** 関数を使用する場合、関数はストリームデータ（`cwd`、`base`、`path` プロパティを含むオブジェクト）を受け取ります。 |
| dot | boolean | false | true の場合、`.gitignore` のようなドットファイルに対する globs を比較します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| silent | boolean | true | true の場合、`stderr` に警告が出力されないようにします。<br>**注意：** このオプションは [node-glob][node-glob-external] へ直接渡されますが、`false` ではなく `true` が初期値になります。 |
| mark | boolean | false | true の場合、ディレクトリマッチングのために `/` 文字が追加されます。パスはパイプライン内で正規化されるため、通常は必要ありません。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nosort | boolean | false | true の場合、glob 結果の並び替えを無効にします。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| stat | boolean | false | true の場合、`fs.stat()` はすべての結果で呼び出されます。これにより余分なオーバーヘッドが追加されるため、通常は使用すべきではありません。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| strict | boolean | false | true の場合、ディレクトリを読み込もうとしたときに不明な問題が発生した場合エラーが投げられます。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nounique | boolean | false | false の場合、結果セット内のファイルの重複を防ぎます。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| debug | boolean | false | true の場合、デバッグ情報がコマンドラインに記録されます。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nobrace | boolean | false | true の場合、`{a,b}` や `{1..3}` のようなブレースセットの拡張を回避します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| noglobstar | boolean | false | true の場合、ダブルスター glob 文字をシングルスター glob 文字として扱います。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| noext | boolean | false | true の場合、`+(ab)` のような [extglob][extglob-docs] パターンのマッチングを回避します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nocase | boolean | false | true の場合、ケースインセンティブなマッチングを行います。<br>**注意：** ケースインセンティブなファイルシステムで、デフォルトで非マジックパターンを照合します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| matchBase | boolean | false | true かつ glob が `/` 文字を含まない場合、すべてのディレクトリを走査しその glob を見つけます。例えば、`*.js` は `**/*.js` と同等として扱われます。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nodir | boolean | false | true の場合、ディレクトリではなくファイルのみを照合します。<br>**注意：** ディレクトリのみを照合させるには、glob を `/` で終わらせます。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| ignore | string<br>array | | マッチングから除外する glob。このオプションは否定 `globs` と組み合わされます。<br>**注意：:** これらの glob は、他の設定に関係なく、常にドットファイルを照合します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| follow | boolean | false | true の場合、`**` glob を展開するとき、シンボリックリンクされたディレクトリを横断します。<br>**注意：** これは循環リンクで問題を起こす可能性があります。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| realpath | boolean | false | true の場合、`fs.realpath()` はすべての結果で呼び出されます。これにより、リンクがダングリングする場合があります。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| cache | object | | 以前作成されたキャッシュオブジェクト。いくつかのファイルシステム呼び出しを回避します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| statCache | object | | 以前作成された `fs.Stat` の結果のキャッシュ。いくつかのファイルシステム呼び出しを回避します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| symlinks | object | | 以前作成されたシンボリックリンクのキャッシュ。いくつかのファイルシステム呼び出しを回避します。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |
| nocomment | boolean | false | false の場合、glob のはじめの `#` 文字をコメントとして扱います。<br>_このオプションは [node-glob][node-glob-external] へ直接渡されます。_ |

## ソースマップ

ソースマップのサポートは `src()` と `dest()` に直接組み込まれていますが、デフォルトで無効になっています。インラインまたは外部のソースマップを生成するために有効にしてください。

インラインソースマップ：
```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: true }));
```

外部ソースマップ：
```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');

src('input/**/*.js', { sourcemaps: true })
  .pipe(uglify())
  .pipe(dest('output/', { sourcemaps: '.' }));
```

[sourcemaps-section]: #sourcemaps
[options-section]: #options
[vinyl-concepts]: ../api/concepts.md#vinyl
[glob-base-concepts]: ../api/concepts.md#glob-base
[globs-concepts]: ../api/concepts.md#globs
[extglob-docs]: ../documentation-missing.md
[node-glob-external]: https://github.com/isaacs/node-glob
[glob-stream-external]: https://github.com/gulpjs/glob-stream
