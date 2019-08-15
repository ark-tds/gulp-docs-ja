# バッファ（メモリの内容）からストリームを作成する

内容が物理ファイルではなく変数にあるファイルでストリームを開始する必要があるかもしれません。言い換えれば、`gulp.src()` を使用せずに 'gulp' ストリームを開始する方法です。

例えば、js ライブラリファイルを含むディレクトリと、いくつかのモジュールのバージョンを含む他のディレクトリがあるとします。ビルドの目的は、すべてのライブラリと連結されたモジュールのバージョンを含みながら、バージョンごとに1つの js ファイルを作成することです。

論理的に、次のように分類します。

* ライブラリファイルを読み込む
* ライブラリファイルの内容を連結する
* バージョンファイルを読み込む
* 各バージョンファイルに対して、ライブラリの内容とバージョンファイルの内容を連結する
* 各バージョンファイルに対して、ファイル内に結果を出力する

次のファイル構造から

```sh
├── libs
│   ├── lib1.js
│   └── lib2.js
└── versions
    ├── version.1.js
    └── version.2.js
```

次の結果を得ることを想像してください。

```sh
└── output
    ├── version.1.complete.js # lib1.js + lib2.js + version.1.js
    └── version.2.complete.js # lib1.js + lib2.js + version.2.js
```

これを行うシンプルでモジュラーな方法は次のとおりです。

```js
var gulp = require('gulp');
var source = require('vinyl-source-stream');
var vinylBuffer = require('vinyl-buffer');
var tap = require('gulp-tap');
var concat = require('gulp-concat');
var size = require('gulp-size');
var path = require('path');
var es = require('event-stream');

var memory = {}; // we'll keep our assets in memory

// task of loading the files' contents in memory
gulp.task('load-lib-files', function() {
  // read the lib files from the disk
  return gulp.src('src/libs/*.js')
    // concatenate all lib files into one
    .pipe(concat('libs.concat.js'))
    // tap into the stream to get each file's data
    .pipe(tap(function(file) {
      // save the file contents in memory
      memory[path.basename(file.path)] = file.contents.toString();
    }));
});

gulp.task('load-versions', function() {
  memory.versions = {};
  // read the version files from the disk
  return gulp.src('src/versions/version.*.js')
  // tap into the stream to get each file's data
  .pipe( tap(function(file) {
    // save the file contents in the assets
    memory.versions[path.basename(file.path)] = file.contents.toString();
  }));
});

gulp.task('write-versions', function() {
  // we store all the different version file names in an array
  var availableVersions = Object.keys(memory.versions);
  // we make an array to store all the stream promises
  var streams = [];

  availableVersions.forEach(function(v) {
    // make a new stream with fake file name
    var stream = source('final.' + v);

    var streamEnd = stream;

    // we load the data from the concatenated libs
    var fileContents = memory['libs.concat.js'] +
      // we add the version's data
      '\n' + memory.versions[v];

    // write the file contents to the stream
    stream.write(fileContents);

    process.nextTick(function() {
      // in the next process cycle, end the stream
      stream.end();
    });

    streamEnd = streamEnd
    // transform the raw data into the stream, into a vinyl object/file
    .pipe(vinylBuffer())
    //.pipe(tap(function(file) { /* do something with the file contents here */ }))
    .pipe(gulp.dest('output'));

    // add the end of the stream, otherwise the task would finish before all the processing
    // is done
    streams.push(streamEnd);

  });

  return es.merge.apply(this, streams);
});

//============================================ our main task
gulp.task('default', gulp.series(
    // load the files in parallel
    gulp.parallel('load-lib-files', 'load-versions'),
    // ready to write once all resources are in memory
    'write-versions'
  )
);

//============================================ our watcher task
// only watch after having run 'default' once so that all resources
// are already in memory
gulp.task('watch', gulp.series(
  'default',
  function() {
    gulp.watch('./src/libs/*.js', gulp.series(
      'load-lib-files',
      'write-versions'
    ));

    gulp.watch('./src/versions/*.js', gulp.series(
      'load-lib-files',
      'write-versions'
    ));
  }
));
```
