<!-- front-matter
id: async-completion
title: Async Completion
hide_title: true
sidebar_label: Async Completion
-->

# 非同期完了
Node のライブラリは様々な方法で非同期に処理を行います。最も一般的なパターンは [エラーファーストコールバック][node-api-error-first-callbacks] ですが、[ストリーム][stream-docs] や [プロミス][promise-docs]、[イベントエミッター][event-emitter-docs]、[子プロセス][child-process-docs]、[observables][observable-docs] にも遭遇するかもしれません。gulp タスクは、これらすべての非同期性の種類を正規化します。

## タスクの完了を通知する

ストリームやプロミス、イベントエミッター、子プロセス、observable がタスクから返されたとき、成功またはエラーによって gulp に続行するか終了するか通知されます。タスクがエラーを起こすと、gulp は即座に終了して、エラーを表示します。

`series()` でタスクを合成するとき、エラーが発生すると合成を終了し、それ以上のタスクは実行されません。`parallel()` でタスクを合成するとき、エラーが発生すると合成は終了しますが、他の並列タスクは完了するかもしれないし、完了しないかもしれません。

### ストリームを返す

```js
const { src, dest } = require('gulp');

function streamTask() {
  return src('*.js')
    .pipe(dest('output'));
}

exports.default = streamTask;
```

### プロミスを返す

```js
function promiseTask() {
  return Promise.resolve('the value is ignored');
}

exports.default = promiseTask;
```

### イベントエミッターを返す

```js
const { EventEmitter } = require('events');

function eventEmitterTask() {
  const emitter = new EventEmitter();
  // Emit has to happen async otherwise gulp isn't listening yet
  setTimeout(() => emitter.emit('finish'), 250);
  return emitter;
}

exports.default = eventEmitterTask;
```

### 子プロセスを返す

```js
const { exec } = require('child_process');

function childProcessTask() {
  return exec('date');
}

exports.default = childProcessTask;
```

### observable を返す

```js
const { Observable } = require('rxjs');

function observableTask() {
  return Observable.of(1, 2, 3);
}

exports.default = observableTask;
```

### エラーファーストコールバックの使用

タスクから何も返されない場合、完了を通知するためにエラーファーストコールバックを使用しなければなりません。コールバックは唯一の引数としてタスクに渡されます。次の例では `cb()` という名前です。

```js
function callbackTask(cb) {
  // `cb()` should be called by some async work
  cb();
}

exports.default = callbackTask;
```

エラーファーストコールバックを用いてタスク内で起こったエラーを gulp に示すためには、唯一の引数として `Error` とともにそれを呼びます。

```js
function callbackError(cb) {
  // `cb()` should be called by some async work
  cb(new Error('kaboom'));
}

exports.default = callbackError;
```

しかしながら、コールバック自身を呼ぶ代わりに、それを他の API に通すこともよくあります。

```js
const fs = require('fs');

function passingCallback(cb) {
  fs.access('gulpfile.js', cb);
}

exports.default = passingCallback;
```

## No 同期的なタスク

同期的なタスクはもはやサポートされていません。それらはしばしば、タスクからストリームを返し忘れるようなデバッグが困難なとらえがたいミスにつながりました。

_"Did you forget to signal async completion?"_ （非同期完了の通知を忘れましたか？）という警告を見たとき、上記のテクニックのいずれも使用されていません。この問題を解決するためには、エラーファーストコールバックを使用するか、ストリームやプロミス、イベントエミッター、子プロセス、observable を返す必要があります。

## async/await の使用

前述のオプションをどれも使用しない場合、タスクを [`async` 関数][async-await-docs] として定義することができます。これはプロミスの中にタスクをラップします。これにより、`await` と他の同期的なコードを用いてプロミスを同期的に処理することができます。

```js
const fs = require('fs');

async function asyncAwaitTask() {
  const { version } = JSON.parse(fs.readFileSync('package.json', 'utf8'));
  console.log(version);
  await Promise.resolve('some result');
}

exports.default = asyncAwaitTask;
```

[node-api-error-first-callbacks]: https://nodejs.org/api/errors.html#errors_error_first_callbacks
[stream-docs]: https://nodejs.org/api/stream.html#stream_stream
[promise-docs]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
[event-emitter-docs]: https://nodejs.org/api/events.html#events_events
[child-process-docs]: https://nodejs.org/api/child_process.html#child_process_child_process
[observable-docs]: https://github.com/tc39/proposal-observable/blob/master/README.md
[async-await-docs]: https://developers.google.com/web/fundamentals/primers/async-functions
