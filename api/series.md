<!-- front-matter
id: series
title: series()
hide_title: true
sidebar_label: series()
-->

# series()

タスク関数かつ/または合成された操作を、より大きな操作に組み合わせて、順番に次々に実行します。`series()` と `parallel()` を使用した合成操作のネストの深さに制限はありません。


## 使用法

```js
const { series } = require('gulp');

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.build = series(javascript, css);
```

## シグネチャ

```js
series(...tasks)
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| tasks<br />**（必須）** | function<br />string | 任意の数のタスク関数を個々の引数として渡すことができます。以前タスクを登録した場合、文字列を使用できますが、これは推奨されません。 |

### 返り値

タスクとして登録されるか、他の `series` かつ/または `parallel` 合成内にネストされる合成操作。

合成操作が実行されるとき、すべてのタスクは順番に実行されます。1つのタスクでエラーが発生した場合、後続のタスクは実行されません。

### エラー

渡されるタスクがない場合、"One or more tasks should be combined using series or parallel" というメッセージとともにエラーが投げられます。

個々のタスクまたは未登録のタスクが渡された場合、"Task never defined" というメッセージとともにエラーが投げられます。

## 前方参照

前方参照は、文字列参照を使用して、まだ登録されていないタスクを合成する場合です。これは古いバージョンでは一般的な方法でしたが、タスクの実行時間を高速化し、名前付き関数の使用を促進するためにこの機能は削除されました。

新しいバージョンでは、前方参照を使用しようとすると、"Task never defined" というメッセージとともにエラーが発生します。これは、タスクの登録のために `exports` を使用して、*かつ*文字列でタスクを合成しようとするとき、発生する場合があります。この状況では、文字列参照の代わりに名前付き関数を使用してください。

移行中に、[前方参照レジストリ][undertaker-forward-reference-external]を使用する必要があるかもしれません。これにより、すべてのタスク参照に追加のクロージャが追加され、ビルドが劇的に遅くなります。**この修正に非常に長い間依存しないでください。**

## タスクの重複を避ける

合成操作が実行されるとき、各タスクは提供されるたびに実行されます。

2つの異なる合成で参照される `clean` タスクは2回実行され、望ましくない結果につながります。代わりに、最後の合成で指定されるように `clean` タスクをリファクタリングしてください。

このようなコードがある場合、
```js
// This is INCORRECT
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

exports.build = parallel(css, javascript);
```

このように移行してください。
```js
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

exports.build = series(clean, parallel(css, javascript));
```

[undertaker-forward-reference-external]: https://github.com/gulpjs/undertaker-forward-reference
