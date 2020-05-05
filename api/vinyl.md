<!-- front-matter
id: vinyl
title: Vinyl
hide_title: true
sidebar_label: Vinyl
-->

# Vinyl

仮想ファイル形式。ファイルが `src()` によって読み込まれると、パスやコンテンツ、その他のメタデータを含む、ファイルを表す Vinyl オブジェクトが生成されます。

Vinyl オブジェクトは[プラグイン][using-plugins-docs]を使用して変換を適用できます。また、`dest()` を使用してファイルシステムに保存することもできます。

`src()` で生成する代わりに、独自の Vinyl オブジェクトを作成する場合、以下の使用法に示すように、外部の `vinyl` モジュールを使用してください。

## 使用法

```js
const Vinyl = require('vinyl');

const file = new Vinyl({
  cwd: '/',
  base: '/test/',
  path: '/test/file.js',
  contents: new Buffer('var x = 123')
});

file.relative === 'file.js';

file.dirname === '/test';
file.dirname = '/specs';
file.path === '/specs/file.js';

file.basename === 'file.js';
file.basename = 'file.txt';
file.path === '/specs/file.txt';

file.stem === 'file';
file.stem = 'foo';
file.path === '/specs/foo.txt';
file.extname === '.txt';
file.extname = '.js';
file.path === '/specs/foo.js';
```

## シグネチャ

```js
new Vinyl([options])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| options | object | 詳細は以下の[オプション][options-section]にあります。 |

### 返り値

単一の仮想ファイルを表す Vinyl クラスのインスタンス。詳細は以下の[Vinyl インスタンス][vinyl-instance-section]にあります。

### エラー

渡されたオプションが[インスタンスプロパティの定義][instance-properties-section]に準拠していない場合（`path` が number に設定されている場合など）、表に定義されているとおりに投げられます。

### オプション

| 名前 | 型 | 初期値 | 備考 |
|:-------:|:------:|-----------|--------|
| cwd | string | `process.cwd()` | 相対パスから導出されるディレクトリ。[正規化][normalization-and-concatenation-section]され、末尾のセパレータが削除されます。 |
| base | string | | `relative` なインスタンスプロパティの計算に使用されます。設定されていない場合、`cwd` の値にフォールバックされます。通常、[glob base][glob-base-concepts] に設定されます。[正規化][normalization-and-concatenation-section]され、末尾のセパレータが削除されます。 |
| path | string | | 完全な絶対パス。[正規化][normalization-and-concatenation-section]され、末尾のセパレータが削除されます。 |
| history | array | `[ ]` | Vinyl インスタンスの `history` を事前入力するためのパスの配列。通常、以前の Vinyl オブジェクトから新しい Vinyl オブジェクトを派生させることで得られます。`path` と `history` の両方が渡された場合、`path` は `history` に追加されます。各項目は[正規化][normalization-and-concatenation-section]され、末尾のセパレータが削除されます。 |
| stat | object | | `fs.Stats` のインスタンス。通常、ファイルに対して `fs.stat()` を呼び出した結果です。Vinyl オブジェクトがディレクトリまたはシンボリックリンクのどちらを表すのか判定するために使用されます。 |
| contents | ReadableStream<br />Buffer<br />`null` | `null` | ファイルの内容。`contents` が ReadableStream の場合、[cloneable-readable][cloneable-readable-external] なストリームにラップされます。 |

`options` の他のプロパティは、Vinyl インスタンスに直接割り当てられます。

```js
const Vinyl = require('vinyl');

const file = new Vinyl({ foo: 'bar' });
file.foo === 'bar';
```

## Vinyl インスタンス

Vinyl オブジェクトの各インスタンスは、仮想ファイルに関する情報にアクセスしたり、変更したりするためのプロパティとメソッドをもちます。

### インスタンスプロパティ

内部的に管理されている全てのパス（`contents` と `stat` を除くすべてのインスタンスプロパティ）は正規化され、末尾のセパレーターが削除されます。詳細は[正規化と連結][normalization-and-concatenation-section]を参照してください。

| プロパティ | 型 | 説明 | スロー |
|:-----------:|:------:|----------------|----------|
| contents | ReadableStream<br />Buffer<br />`null` | 仮想ファイルの内容を取得、設定します。ReadableStream に設定すると、[cloneable-readable][cloneable-readable-external] なストリームにラップされます。 | ReadableStream、Buffer または `null` 以外の値を設定した場合。 |
| stat | object | [`fs.Stats`][fs-stats-concepts] のインスタンスを取得、設定します。Vinyl オブジェクトがディレクトリまたはシンボリックリンクのどちらを表すのか判定するために使用されます。 | |
| cwd | string | 現在のワーキングディレクトリを取得、設定します。相対パスの導出に使用されます。 | 空の文字列または文字列以外の値を設定した場合。 |
| base | string | ベースディレクトリを取得、設定します。`relative` インスタンスプロパティを計算するために使用されます。`src()` によって生成された Vinyl オブジェクト上で、[glob base][glob-base-concepts] に設定されます。`null` または `undefined` が設定された場合、`cwd` インスタンスプロパティの値にフォールバックされます。 | 空の文字列または（`null` または `undefined` を除く）文字列以外の値を設定した場合。 |
| path | string | 完全な絶対パスを取得、設定します。現在の `path` と異なる値を設定すると、`history` インスタンスプロパティに新しいパスが追加されます。 | 文字列以外の値が設定された場合。 |
| history | array | Vinyl オブジェクトが割り当てられたすべての `path` の値の配列。最初の要素は元のパスで、最後の要素は現在のパスです。このプロパティとその要素は読み取り専用として扱われ、`path` インスタンスプロパティを設定することでのみ、間接的に変更されます。 | |
| relative | string | `base` と `path` インスタンスプロパティの間の相対パス部分を取得します。 | 任意の値を設定した場合。`path` が利用できないときにアクセスした場合。 |
| dirname | string | `path` インスタンスプロパティのディレクトリを取得、設定します。 | `path` が利用できないときにアクセスした場合。 |
| stem | string | `path` インスタンスプロパティの stem（拡張子なしのファイル名）を取得、設定します。 | `path` が利用できないときにアクセスした場合。 |
| extname | string | `path` インスタンスプロパティの拡張子を取得、設定します。 | `path` が利用できないときにアクセスした場合。 |
| basename | string | `path` インスタンスプロパティのファイル名（`stem + extname`）を取得、設定します。 | `path` が利用できないときにアクセスした場合。 |
| symlink | string | シンボリックリンクの参照パスを取得、設定します。 | 文字列以外の値が設定された場合。 |

### インスタンスメソッド

| メソッド | 返り値の型 | 返り値 |
|:----------:|:--------------:|--------|
| `isBuffer()` | boolean | `contents` インスタンスプロパティが Buffer の場合、true を返します。 |
| `isStream()` | boolean | `contents` インスタンスプロパティが Stream の場合、true を返します。 |
| `isNull()` | boolean | `contents` インスタンスプロパティが `null` の場合、true を返します。 |
| `isDirectory()` | boolean | インスタンスがディレクトリを表す場合、true を返します。`isNull()` が true を返し、`stat` インスタンスプロパティがオブジェクトで、`stat.isDirectory()` が true を返す場合、インスタンスはディレクトリとみなされます。これは、Vinyl オブジェクトが有効な（または適切にモックされた）`fs.Stats` オブジェクトで構築されたことを前提としています。 |
| `isSymbolic()` | boolean | インスタンスがシンボリックリンクを表す場合、true を返します。`isNull()` が true を返し、`stat` インスタンスプロパティがオブジェクトで、`stat.isSymbolicLink()` が true を返す場合、インスタンスはシンボリックとみなされます。これは、Vinyl オブジェクトが有効な（または適切にモックされた）`fs.Stats` オブジェクトで構築されたことを前提としています。 |
| `clone([options])` | object | すべてのプロパティが複製された新しい Vinyl オブジェクト。デフォルトでは、カスタムプロパティはディープクローンされます。`deep` オプションが false の場合、カスタムプロパティはシャロークローンされます。`contents` オプションが false で、`contents` インスタンスプロパティが Buffer の場合、Buffer は複製される代わりに再利用されます。 |
| `inspect()` | string | Vinyl オブジェクトのフォーマットされた説明を返します。Node の console.log によって自動的に呼び出されます。 |

## 正規化と連結

すべてのパスプロパティは、そのセッターによって正規化されます。`path.join()` を使用する代わりに、`/` で連結すると、すべてのプラットフォームで正規化が適切に行われます。 決して `\` で連結しないでください。これは、POSIX システムで有効なファイル名文字列です。

```js
const file = new File();
file.path = '/' + 'test' + '/' + 'foo.bar';

console.log(file.path);
// posix => /test/foo.bar
// win32 => \\test\\foo.bar
```

[options-section]: #options
[vinyl-instance-section]: #vinyl-instance
[instance-properties-section]: #instance-properties
[normalization-and-concatenation-section]: #normalization-and-concatenation
[glob-base-concepts]: ../api/concepts.md#glob-base
[fs-stats-concepts]: ../api/concepts.md#file-system-stats
[using-plugins-docs]: ../getting-started/7-using-plugins.md
[cloneable-readable-external]: https://github.com/mcollina/cloneable-readable
