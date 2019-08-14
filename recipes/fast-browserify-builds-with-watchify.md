# watchify を用いた高速な browserify のビルド

[browserify](https://github.com/browserify/browserify) プロジェクトが拡大し始めると、バンドル時間が徐々に長くなります。1秒で開始する場合もありますが、特に大きなプロジェクトでビルドされるまで30秒待機する可能性もあります。

これが、[substack](https://github.com/substack) が [watchify](https://github.com/browserify/watchify) を書いた理由です。watchify は、ファイルの変更を監視し、**必要なものだけを再ビルドする**永続的な browserify バンドラーです。この方法では、最初のビルドにはまだ30秒かかるかもしれませんが、後続のビルドは100ミリ秒未満で実行できます。これは大きな改善です。

Watchify には gulp プラグインがなく、その必要もありません。[vinyl-source-stream](https://github.com/hughsk/vinyl-source-stream) を使用すると、gulp パイプラインにそのバンドルストリームをパイプすることができます。

``` javascript
'use strict';

var watchify = require('watchify');
var browserify = require('browserify');
var gulp = require('gulp');
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');
var log = require('gulplog');
var sourcemaps = require('gulp-sourcemaps');
var assign = require('lodash.assign');

// add custom browserify options here
var customOpts = {
  entries: ['./src/index.js'],
  debug: true
};
var opts = assign({}, watchify.args, customOpts);
var b = watchify(browserify(opts));

// add transformations here
// i.e. b.transform(coffeeify);

gulp.task('js', bundle); // so you can run `gulp js` to build the file
b.on('update', bundle); // on any dep update, runs the bundler
b.on('log', log.info); // output build logs to terminal

function bundle() {
  return b.bundle()
    // log errors if they happen
    .on('error', log.error.bind(log, 'Browserify Error'))
    .pipe(source('bundle.js'))
    // optional, remove if you don't need to buffer file contents
    .pipe(buffer())
    // optional, remove if you dont want sourcemaps
    .pipe(sourcemaps.init({loadMaps: true})) // loads map from browserify file
       // Add transformation tasks to the pipeline here.
    .pipe(sourcemaps.write('./')) // writes .map file
    .pipe(gulp.dest('./dist'));
}
```
