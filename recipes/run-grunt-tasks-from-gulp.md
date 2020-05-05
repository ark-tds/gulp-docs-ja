# Gulp から Grunt タスクを実行する

Gulp 内から Grunt タスク / Grunt プラグインを実行することができます。これは、Grunt から Gulp への段階的な移行中、または必要な特定のプラグインがある場合に役立ちます。ここで述べたアプローチでは Grunt CLI と Gruntfile は必須ではありません。

**このアプローチは Grunt >=1.0.0 を要求します**

非常にシンプルな例 `gulpfile.js`

```js
// npm install gulp grunt grunt-contrib-copy --save-dev

var gulp = require('gulp');
var grunt = require('grunt');

grunt.initConfig({
    copy: {
        main: {
            src: 'src/*',
            dest: 'dest/'
        }
    }
});
grunt.loadNpmTasks('grunt-contrib-copy');

gulp.task('copy', function (done) {
    grunt.tasks(
        ['copy:main'],    //you can add more grunt tasks in this array
        {gruntfile: false}, //don't look for a Gruntfile - there is none. :-)
        function () {done();}
    );
});

```

さぁ `gulp copy` タスクを開始しましょう。

前述のアプローチにより、grunt タスクは gulp のタスクシステム内に登録されます。**grunt タスクは（gulp と異なり）通常ブロックするため、grunt タスクが完了するまで他のタスク（gulp タスクでさえも）を実行できないことに注意してください。**


### 代替手法に関するいくつかの文句

*gulp フレンドリーな*ノードモジュール `gulp-grunt` [利用可能](https://www.npmjs.org/package/gulp-grunt)があり、これは異なるアプローチを取ります。これは子プロセスを作成し、それらの中で grunt タスクが実行されます。これを動作させるにはいくつかの制限があります。

* 現時点では、`gulp-grunt` を介してオプション / cli の引数などを grunt タスクに渡すことができません。
* すべての grunt タスクは個別の Gruntfile 内で定義されなければなりません。
* Grunt CLI をインストールする必要があります。
* いくつかの grunt タスクの出力は不正な形式（例えば、カラーコード）になります。
