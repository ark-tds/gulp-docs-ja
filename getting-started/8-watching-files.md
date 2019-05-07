<!-- front-matter
id: watching-files
title: Watching Files
hide_title: true
sidebar_label: Watching Files
-->

# ファイルの監視

`watch()` API はファイルシステムウォッチャーを用いて、[glob][globs-docs] を[タスク][creating-tasks-docs]に接続します。glob に一致するファイルの変更を監視し、変更が起こったときタスクを実行します。タスクが[非同期完了][async-completion-doc]を通知しなければ、2回目に決して実行されません。

この API は、最も一般的に使用されるデフォルトに基づいたビルトインの遅延とキューを提供します。

```js
const { watch, series } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.default = function() {
  // You can use a single task
  watch('src/*.css', css);
  // Or a composed task
  watch('src/*.js', series(clean, javascript));
};
```

## 警告：同期を避けてください

監視タスクは、タスクシステムに登録されたタスクのように同期的にすることはできません。同期的なタスクを渡すと、完了を判断できず、タスクは二度と実行されません。まだ実行中であるとみなされます。

ファイルウォッチャーは Node プロセスを実行し続けるため、エラーや警告はメッセージは表示されません。プロセスが終了しないので、タスクが完了したのか、単に実行に非常に、非常に長い時間がかかっているのかどうかを判断できません。

## 監視イベント

デフォルトで、ファイルが作成、変更、削除されるたびに、ウォッチャーはタスクを実行します。
異なるイベントが必要になった場合、`watch()` を呼ぶとき `events` オプションを使用することができます。利用可能なイベントは `'add'`、`'addDir'`、`'change'`、`'unlink'`、`'unlinkDir'`、`'ready'`、`'error'` です。さらに、`'ready'` と `'error'` 以外のすべてのイベントを表す `'all'` が利用可能です。

```js
const { watch } = require('gulp');

exports.default = function() {
  // All events will be watched
  watch('src/*.js', { events: 'all' }, function(cb) {
    // body omitted
    cb();
  });
};
```

## 初期実行

`watch()` を呼んでも、タスクは実行されず、代わりに最初のファイルの変更を待ちます。

最初のファイルの変更の前にタスクを実行するためには、`ignoreInitial` オプションを `false` に設定してください。

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task will be executed upon startup
  watch('src/*.js', { ignoreInitial: false }, function(cb) {
    // body omitted
    cb();
  });
};
```

## キュー

各 `watch()` は、現在実行しているタスクが再度同時に実行されないことを保証しています。監視タスクの実行中にファイルの変更が起こると、そのタスクが終了したときに実行するために別の実行がキューに入ります。1度にキューに入れることができる実行は1つだけです。

キューを無効にするためには、`queue` オプションを `false` に設定してください。

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task will be run (concurrently) for every change made
  watch('src/*.js', { queue: false }, function(cb) {
    // body omitted
    cb();
  });
};
```

## 遅延

ファイルの変更が起こっても、監視タスクは200msの遅延が経過するまで実行されません。これは、検索置換のように、1度に多くのファイルが変更されているときタスクがそうそうに開始されるのを防ぐためです。

遅延時間を調整するには、`delay` オプションを正の整数に設定してください。

```js
const { watch } = require('gulp');

exports.default = function() {
  // The task won't be run until 500ms have elapsed since the first change
  watch('src/*.js', { delay: 500 }, function(cb) {
    // body omitted
    cb();
  });
};
```

## 監視インスタンスの使用

この機能を使うことはおそらくないでしょうが、パスやメタデータへのアクセスのように、変更されたファイルを完全に制御する必要がある場合は、`watch()` から返される [chokidar][chokidar-module-package] インスタンスを使用してください。

__注意__：返された chokidar インスタンスには、キューや遅延、非同期完了の機能はありません。

## オプションの依存関係

gulp は Mac 特有のファイルウォッチャーである [fsevents][fsevents-package] と呼ばれるオプションの依存関係をもちます。fsevents のインストールに関する警告、_"npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents"_、が表示された場合、問題はありません。fsevents のインストールがスキップされた場合、ファオールバックウォッチャーが使用され、gulpfile 内で起こったエラーはこの警告に関連しません。

[globs-docs]: ../getting-started/6-explaining-globs.md
[creating-tasks-docs]: ../getting-started/3-creating-tasks.md
[async-completion-doc]: ../getting-started/4-async-completion.md
[chokidar-module-package]: https://www.npmjs.com/package/chokidar
[fsevents-package]: https://www.npmjs.com/package/fsevents
