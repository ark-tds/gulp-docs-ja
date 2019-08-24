<!-- front-matter
id: symlink
title: symlink()
hide_title: true
sidebar_label: symlink()
-->

# symlink()

[Vinyl][vinyl-concepts] オブジェクトをファイルシステムへリンクするためのストリームを作成する。

## 使用法

```js
const { src, symlink } = require('gulp');

function link() {
  return src('input/*.js')
    .pipe(symlink('output/'));
}

exports.link = link;
```

## シグネチャ

```js
symlink(directory, [options])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:-----:|--------|
| directory<br>**（必須）** | string<br>function | シンボリックリンクが作成される出力ディレクトリのパス。関数が使用される場合、その関数は各 Vinyl オブジェクトとともに呼び出され、文字列のディレクトリパスを返さなければならない。 |
| options | object | 詳細は以下の[オプション][options-section]にて説明します。 |

### 返り値

ファイルシステム上でシンボリックリンクを作成するために、パイプラインの途中または最後で使用できるストリーム。Vinyl オブジェクトがストリームを通して渡されるたび、与えられたディレクトリのファイルシステム上のオリジナルファイルへのシンボリックリンクを作成します。

ファイルシステムにシンボリックリンクが作成されるたびに、Vinyl オブジェクトが変更されます。
* `cwd`、`base` および `path` プロパティは、作成されたシンボリックリンクと一致するように更新されます。
* `stat` プロパティは、ファイルシステム上のシンボリックリンクと一致するように更新されます。
* `contents` プロパティは `null` に設定されます。
* `symlink` プロパティは追加されるか、オリジナルパスに置き換えられます。

**注意：** Windows では、デフォルトでジャンクションを使用してディレクトリリンクが作成されます。`useJunctions` オプションはこの動作を無効にします。


### エラー

`directory` が空の文字列の場合、"Invalid symlink() folder argument. Please specify a non-empty string or a function." というメッセージとともにエラーが投げられます。

`directory` が文字列や関数ではない場合、"Invalid symlink() folder argument. Please specify a non-empty string or a function." というメッセージとともにエラーが投げられます。

`directory` が空の文字列や `undefined` を返す関数である場合、"Invalid output folder" というメッセージとともにエラーが投げられます。

### オプション

**関数を受け入れるオプションの場合、渡された関数は各 Vinyl オブジェクトで呼び出され、リストされている別の型の値を返さなければなりません。**

| 名前 | 型 | 初期値 | 備考 |
|:-------:|:------:|-----------|-------|
| cwd | string<br>function | `process.cwd()` | 絶対パスを形成するために相対パスと結合されるディレクトリ。絶対パスでは無視されます。`directory` と `path.join()` の組み合わせを避けるために使用してください。 |
| dirMode | number<br>function | | ディレクトリの作成時に使用されるモード。設定されていない場合、代わりにプロセスのモードが使用されます。 |
| overwrite | boolean<br>function | true | true の場合、同じパスにある既存のファイルを上書きします。 |
| relativeSymlinks | boolean<br>function | false | false の場合、作成されたシンボリックリンクは絶対になります。**注意：** ジャンクションが作成されている場合、それらは絶対でなければならないので、無視されます。 |
| useJunctions | boolean<br>function | true | このオプションは Windows でのみ関連し、他では無視されます。true の場合、ディレクトリシンボリックリンクをジャンクションとして作成します。詳細は以下の [Windows 上のシンボリックリンク][symbolic-links-section]にあります。 |

## Windows 上のシンボリックリンク

Windows 上でシンボリックリンクを作成する場合、`type` 引数が、リンクされるターゲットの種類を指定する Node の `fs.symlink()` メソッドに渡されます。リンクの型は次のように設定されます。
* `'file'` ターゲットが通常のファイルの場合
* `'junction'` ターゲットがディレクトリの場合
* `'dir'` ターゲットがディレクトリで、ユーザーが useJunctions オプションを無効にしている場合

ダングリング（存在しないターゲットを指す）リンクを作成しようとすると、そのリンクの型を自動的に判別できません。これらの場合、ダングリングリンクが `symlink()` または `dest()` のどちらかを介して作成されているかに応じて動作が変わります。

`symlink()` を介して作成されたダングリングリンクの場合、入ってくる Vinyl オブジェクトはターゲットを表すので、その統計が目的のリンクの型を決定します。`isDirectory()` が fakse を返す場合、`'file'` リンクが作成され、そうでない場合、useJunctions オプションの値に応じて `'junction'` または `'dir'` リンクが作成されます。

`dest()` を介して作成されたダングリングリンクの場合、入ってくる Vinyl オブジェクトはリンクを表します。通常は `src(..., { resolveSymlinks: false })` を介してディスクから読み込まれたリンクを表します。この場合、リンクの型は合理的に決定できず、`'file'` の使用を初期値とします。これは、ディレクトリへのダングリングリンクを作成している場合、予期しない動作を引き起こすかもしれません。**このシナリオは避けてください。**

[options-section]: #options
[symbolic-links-section]: #symbolic-links-on-windows
[vinyl-concepts]: ../api/concepts.md#vinyl
