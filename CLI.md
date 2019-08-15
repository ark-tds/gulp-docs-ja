## gulp CLI ドキュメント

### フラグ

gulp には、知っておくべきフラグがほとんどありません。他のすべてのフラグは必要に応じて使用するタスク用です。

- `-v` または `--version` はグローバルとローカルの gulp のバージョンを表示します
- `--require <module path>` は gulpfile を実行する前にモジュールを要求します。これはトランスパイラーだけでなく他のアプリケーションにとっても便利です。複数の `--require` フラグを使用できます。
- `--gulpfile <gulpfile path>` は gulpfile のパスを手動で指定します。複数の gulpfile がある場合に便利です。これにより、CWD も gulpfile に設定されます。
- `--cwd <dir path>` は CWD を手動で指定します。gulpfile の検索はもちろん、すべての必要な相対性はここから始まります。
- `-T` または `--tasks` は読み込まれた gulpfile のタスクの依存ツリーを表示します。タスク名とそれらの[説明](./API.md#fndescription)を含みます。
- `--tasks-simple` は読み込まれた gulpfile のタスクのプレーンテキストな一覧を表示します。
- `--verify` はプロジェクトの package.json で参照されているプラグインをプラグインのブラックリストと照合します。
- `--color` は色のサポートが検出されない場合でも、gulp と gulp プラグインに色を強制的に表示させます。
- `--no-color` は色のサポートが検出された場合でも、gulp と gulp プラグインに色を表示させないようにします。
- `--silent` はすべての gulp のログを無効にします。

CLI は起動された元の cwd である process.env.INIT_CWD を追加します。

#### タスク固有のフラグ

タスク固有のフラグを追加する方法は、この [StackOverflow](https://stackoverflow.com/questions/23023650/is-it-possible-to-pass-a-flag-to-gulp-to-have-it-run-tasks-in-different-ways) のリンクを参照してください。

### タスク

タスクを実行するには `gulp <task> <task>...` を実行します。

複数のタスクがリストされている場合、Gulp はそれらのすべてを並行して実行します。つまり、それらはすべて単一のタスクの依存関係としてリストされているかのように実行されます。

Gulp はコマンドラインにリストされたタスクをシリアル化しません。他の同等のツールを使用すると、ユーザーは `clean` と `build` と名付けたタスクを `gulp clean build` のように実行することを期待するかもしれません。2つのタスクは並行して実行されるので、これは意図した結果になりません。

`gulp` を実行するだけでタスク `default` が実行されます。`default` タスクがない場合、gulp はエラーになります。

### コンパイラー

サポートされている言語の一覧は [interpret](https://github.com/tkellen/node-interpret#jsvariants) にあります。新しい言語のサポートを追加する場合、そこへプルリクエストを送るか、イシューを開いてください。

### 例

#### gulpfile の例

```js
gulp.task('one', function(done) {
  // do stuff
  done();
});

gulp.task('two', function(done) {
  // do stuff
  done();
});

gulp.task('three', three);

function three(done) {
  done();
}
three.description = "This is the description of task three";

gulp.task('four', gulp.series('one', 'two'));

gulp.task('five',
  gulp.series('four',
    gulp.parallel('three', function(done) {
      // do more stuff
      done();
    })
  )
);
```

### `-T` または `--tasks`

コマンド：`gulp -T` または `gulp --tasks`

出力：
```shell
[20:58:55] Tasks for ~\exampleProject\gulpfile.js
[20:58:55] ├── one
[20:58:55] ├── two
[20:58:55] ├── three                                         This is the description of task three
[20:58:55] ├─┬ four
[20:58:55] │ └─┬ <series>
[20:58:55] │   ├── one
[20:58:55] │   └── two
[20:58:55] ├─┬ five
[20:58:55] │ └─┬ <series>
[20:58:55] │   ├─┬ four
[20:58:55] │   │ └─┬ <series>
[20:58:55] │   │   ├── one
[20:58:55] │   │   └── two
[20:58:55] │   └─┬ <parallel>
[20:58:55] │     ├── three
[20:58:55] │     └── <anonymous>
```

### `--tasks-simple`

コマンド：`gulp --tasks-simple`

出力：
```shell
one
two
three
four
five
```
