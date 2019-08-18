<!-- front-matter
id: dest
title: dest()
hide_title: true
sidebar_label: dest()
-->

# dest()

ファイルシステムへ [Vinyl][vinyl-concepts] オブジェクトを書き出すためのストリームを作成します。

## 使用法

```js
const { src, dest } = require('gulp');

function copy() {
  return src('input/*.js')
    .pipe(dest('output/'));
}

exports.copy = copy;
```

## シグネチャ

```js
dest(directory, [options])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:-----:|--------|
| directory<br>**(required)** | string<br>function | ファイルが書き出されるディレクトリのパス。関数が使用される場合、関数は各 Vinyl オブジェクトとともに呼び出され、文字列のディレクトリパスを返さなければなりません。 |
| options | object | 詳細は以下の[オプション][options-section]にあります。 |

### 返り値

ファイルシステムにファイルを作成するために、パイプラインの途中または最後で使用できるストリーム。Vinyl オブジェクトがストリームを通るたびに、その内容やその他の詳細が与えられたディレクトリのファイルシステムに書き出されます。Vinyl オブジェクトに `symlink` プロパティがある場合、その内容が書き出される代わりにシンボリックリンクが作成されます。ファイルの生成後、Vinyl オブジェクトと一致するようにその[メタデータは更新されます][metadata-updates-section]。

ファイルがファイルシステムに作成されるたびに、Vinyl オブジェクトは変更されます。
* `cwd`、`base` および `path` プロパティは、作成されたファイルと一致するように更新されます。
* `stat` プロパティは、ファイルシステム上のファイルと一致するように更新されます。
* `contents` プロパティがストリームである場合、再度読み込みができるようにリセットされます。

### エラー

`directory` が空の文字列の場合、"Invalid dest() folder argument. Please specify a non-empty string or a function." というメッセージとともにエラーが投げられます。

`directory` が文字列や関数ではない場合、"Invalid dest() folder argument. Please specify a non-empty string or a function." というメッセージとともにエラーが投げられます。

`directory` が空の文字列や `undefined` を返す関数である場合、"Invalid output folder" というメッセージとともにエラーが投げられます。

### オプション


**関数を受け入れるオプションの場合、渡された関数は各 Vinyl オブジェクトで呼び出され、リストされている別の型の値を返さなければなりません。**

| 名前 | 型 | 初期値 | 備考 |
|:-------:|:------:|-----------|-------|
| cwd | string<br>function | `process.cwd()` | 絶対パスを形成するために相対パスと結合されるディレクトリ。絶対パスでは無視されます。`directory` と `path.join()` の組み合わせを避けるために使用してください。 |
| mode | number<br>function | Vinyl オブジェクトの `stat.mode` | ファイルの作成時に使用されるモード。設定されておらず、`stat.mode` が見つからない場合、代わりにプロセスのモードが使用されます。 |
| dirMode | number<br>function | | ディレクトリの作成時に使用されるモード。設定されていない場合、代わりにプロセスのモードが使用されます。 |
| overwrite | boolean<br>function | true | true の場合、同じパスにある既存のファイルを上書きします。 |
| append | boolean<br>function | false | true の場合、既存の内容を置換する代わりに、ファイルの最後に内容を追加します。 |
| sourcemaps | boolean<br>string<br>function | false | true の場合、出力ファイルへインラインソースマップを書き出します。`string` パスを指定すると、与えられたパスへ外部ソースマップが書き出されます。 |
| relativeSymlinks | boolean<br>function | false | false の場合、作成されたシンボリックリンクは絶対になります。<br>**注意：** ジャンクションが作成されている場合、それらは絶対でなければならないので、無視されます。 |
| useJunctions | boolean<br>function | true | このオプションは Windows でのみ関連し、他では無視されます。true の場合、ディレクトリシンボリックリンクをジャンクションとして作成します。詳細は以下の [Windows 上のシンボリックリンク][symbolic-links-section]にあります。 |

## メタデータの更新

`dest()` ストリームがファイルを作成するたびに、Vinyl オブジェクトの `mode`、`mtime` および `atime` が作成されたファイルと比較されます。それらが異なる場合、Vinyl オブジェクトのメタデータを反映するために作成されたファイルは更新されます。それらのプロパティが同じである場合、または gulp に変更を行う権限がない場合、その動作は静かにスキップされます。

この機能は Windows または Node の `process.getuid()` または `process.geteuid()` メソッドをサポートしていない他のオペレーティングシステムでは無効です。これは、Windows が `fs.fchmod()` と `fs.futimes()` を使用することで予期しない結果をもたらすためです。

**注意：** `fs.futimes()` メソッドは内部で `mtime` と `atime` のタイムスタンプを秒に変換します。この1000による除算により、32ビットオペレーティングシステム上で精度がいくらか失われてしまうかもしれません。

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

## Windows 上のシンボリックリンク

Windows 上でシンボリックリンクを作成する場合、`type` 引数が、リンクされるターゲットの種類を指定する Node の `fs.symlink()` メソッドに渡されます。リンクの型は次のように設定されます。
* `'file'` ターゲットが通常のファイルの場合
* `'junction'` ターゲットがディレクトリの場合
* `'dir'` ターゲットがディレクトリで、ユーザーが `useJunctions` オプションを無効にしている場合


ダングリング（存在しないターゲットを指す）リンクを作成しようとすると、そのリンクの型を自動的に判別できません。これらの場合、ダングリングリンクが `symlink()` または `dest()` のどちらかを介して作成されているかに応じて動作が変わります。

`symlink()` を介して作成されたダングリングリンクの場合、入ってくる Vinyl オブジェクトはターゲットを表すので、その統計が目的のリンクの型を決定します。`isDirectory()` が fakse を返す場合、`'file'` リンクが作成され、そうでない場合、`useJunctions` オプションの値に応じて `'junction'` または `'dir'` リンクが作成されます。

`dest()` を介して作成されたダングリングリンクの場合、入ってくる Vinyl オブジェクトはリンクを表します。通常は `src(..., { resolveSymlinks: false })` を介してディスクから読み込まれたリンクを表します。この場合、リンクの型は合理的に決定できず、`'file'` の使用を初期値とします。これは、ディレクトリへのダングリングリンクを作成している場合、予期しない動作を引き起こすかもしれません。**このシナリオは避けてください。**

[sourcemaps-section]: #sourcemaps
[symbolic-links-section]: #symbolic-links-on-windows
[options-section]: #options
[metadata-updates-section]: #metadata-updates
[vinyl-concepts]: ../api/concepts.md#vinyl
