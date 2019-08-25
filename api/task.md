<!-- front-matter
id: task
title: task()
hide_title: true
sidebar_label: task()
-->

# task()

**リマインダー**：この API はもはや推奨されるパターンではありません。[タスクをエクスポートしてください][creating-tasks-docs]。

タスクシステム内にタスクを定義します。そのタスクはコマンドラインと `series()`、`parallel()` そして `lastRun()` API からアクセスできます。

## 使用法

タスクとして名前付き関数を登録します。
```js
const { task } = require('gulp');

function build(cb) {
  // body omitted
  cb();
}

task(build);
```

タスクとして無名関数を登録します。
```js
const { task } = require('gulp');

task('build', function(cb) {
  // body omitted
  cb();
});
```

以前登録したタスクを取得します。
```js
const { task } = require('gulp');

task('build', function(cb) {
  // body omitted
  cb();
});

const build = task('build');
```

## シグネチャ

```js
task([taskName], taskFunction)
```

### パラメーター

`taskName` が提供されなかった場合、タスクは、名前付き関数の `name` プロパティまたはユーザー定義された `displayName` プロパティによって参照されます。`displayName` プロパティがない無名関数には、`taskName` パラメーターを使用する必要があります。

登録されたタスクはコマンドラインから実行できるので、タスク名にスペースを使用しないでください。

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| taskName | string | タスクシステム内のタスク関数のエイリアス。`taskFunction` に名前付き関数を使用する場合は不要です。 |
| taskFunction<br>**（必須）** | function | [タスク関数][task-concepts]または `series()` と `parallel()` によって生成される合成タスク。理想的には名前付き関数。[タスクメタデータ][task-metadata-section]を割り当て、コマンドラインへ追加の情報を提供できます。 |

### 返り値

タスクを登録する場合、何も返りません。

タスクを取得する場合、`taskName` として登録されたラップされたタスク（オリジナル関数ではない）が返ります。ラップされたタスクには、オリジナルの関数を返す `unwrap()` メソッドがあります。

### エラー

`taskName` がなく、`taskFunction` が無名であるタスクを登録する場合、"Task name must be specified" というメッセージとともにエラーが投げられます。

## タスクメタデータ

| プロパティ | 型 | 備考 |
|:--------------:|:------:|-------|
| name | string | 名前付き関数の特別なプロパティ。タスクの登録に使用します。<br>**注意：** [`name`][function-name-external] は書き込み可能です。設定まはた変更できません。 |
| displayName | string | `taskFunction` へ割り当てる場合、タスクのエイリアスを作成します。関数名に許可されていない文字を使用する場合、このプロパティを使用してください。 |
| description | string | `taskFunction` へ割り当てる場合、タスクを一覧表示するときコマンドラインによって出力される説明を提供します。 |
| flags | object | `taskFunction` へ割り当てる場合、タスクを一覧表示するときコマンドラインによって出力されるフラグを提供します。オブジェクトのキーはフラグを表し、値はその説明を表します。 |

```js
const { task } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};
clean.displayName = 'clean:all';

task(clean);

function build(cb) {
  // body omitted
  cb();
}
build.description = 'Build the project';
build.flags = { '-e': 'An example flag' };

task(build);
```

[task-metadata-section]: #task-metadata
[task-concepts]: ../api/concepts.md#tasks
[creating-tasks-docs]: ../getting-started/3-creating-tasks.md
[function-name-external]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name
