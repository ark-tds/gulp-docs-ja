<!-- front-matter
id: lastrun
title: lastRun()
hide_title: true
sidebar_label: lastRun()
-->

# lastRun()

現在実行中のプロセスでタスクが正常に完了した最後の時間を取得します。ウォッチャーの実行中に後続のタスクを実行する場合に最も役立ちます。

`src()` と組み合わせると、最後にタスクが正常に完了してから変更されていないファイルをスキップすることで、実行時間をスピードアップするインクリメンタルビルドを可能にします。

## 使用法

```js
const { src, dest, lastRun, watch } = require('gulp');
const imagemin = require('gulp-imagemin');

function images() {
  return src('src/images/**/*.jpg', { since: lastRun(images) })
    .pipe(imagemin())
    .pipe(dest('build/img/'));
}

exports.default = function() {
  watch('src/images/**/*.jpg', images);
};
```


## シグネチャ

```js
lastRun(task, [precision])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| task<br>**（必須）** | function<br>string | タスク関数または登録されたタスクの文字列エイリアス。 |
| precision | number | 初期値：Node v0.10 で `1000`、Node v0.12+ で `0`。詳細は以下の[タイムスタンプ精度][timestamp-precision-section]にあります。 |

### 返り値

タスクの最後の完了時間に一致するタイムスタンプ（ミリ秒）。タスクが実行されていないか失敗した場合、`undefined` を返します。

無効な状態のキャッシュを避けるため、タスクがエラーを起こした場合、返り値は `undefined` になります。

### エラー

文字列や関数以外の値とともに呼ばれたとき、"Only functions can check lastRun" というメッセージとともにエラーが投げられます。

非拡張関数で呼び出され、Node に WeakMap がない場合、"Only extensible functions can check lastRun" というメッセージとともにエラーが投げられます。

## タイムスタンプ精度

タイムスタンプの精度には適切な初期値がありますが、それらは `precision` パラメーターを使用して丸めることができます。ファイルシステムまたは Node のバージョンがファイル時間属性で精度を損失している場合に役立ちます。


* `lastRun(someTask)` は 1426000001111 を返します
* `lastRun(someTask, 100)` は 1426000001100 を返します
* `lastRun(someTask, 1000)` は 1426000001000 を返します

ファイルの [mtime stat][fs-stats-concepts] 精度は、使用している Node のバージョンかつ/またはファイルシステムによって異なる場合があります。


| プラットフォーム | 精度 |
|:-----------:|:------------:|
| Node v0.10 | 1000ms |
| Node v0.12+ | 1ms |
| FAT32 ファイルシステム | 2000ms |
| HFS+ または Ext3 ファイルシステム | 1000ms |
| Node v0.10 を使用した NTFS | 1s |
| Node 0.12+ を使用した NTFS | 100ms |
| Node v0.10 を使用した Ext4 | 1000ms |
| Node 0.12+ を使用した Ext4 | 1ms |


[timestamp-precision-section]: #timestamp-precision
[fs-stats-concepts]: ../api/concepts.md#file-system-stats
