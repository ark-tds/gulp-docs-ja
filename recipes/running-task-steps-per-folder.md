# フォルダごとのファイルの生成

いくつかのフォルダがあり、それぞれにいくつかのタスクを実行したい場合、例えば、

```
/scripts
/scripts/jquery/*.js
/scripts/angularjs/*.js
```

そして、最終的に次のようにしたい場合、

```
/scripts
/scripts/jquery.min.js
/scripts/angularjs.min.js
```

次のようなことをする必要があります。

``` javascript
var fs = require('fs');
var path = require('path');
var merge = require('merge-stream');
var gulp = require('gulp');
var concat = require('gulp-concat');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');

var scriptsPath = 'src/scripts';

function getFolders(dir) {
    return fs.readdirSync(dir)
      .filter(function(file) {
        return fs.statSync(path.join(dir, file)).isDirectory();
      });
}

gulp.task('scripts', function(done) {
   var folders = getFolders(scriptsPath);
   if (folders.length === 0) return done(); // nothing to do!
   var tasks = folders.map(function(folder) {
      return gulp.src(path.join(scriptsPath, folder, '/**/*.js'))
        // concat into foldername.js
        .pipe(concat(folder + '.js'))
        // write to output
        .pipe(gulp.dest(scriptsPath))
        // minify
        .pipe(uglify())
        // rename to folder.min.js
        .pipe(rename(folder + '.min.js'))
        // write to output again
        .pipe(gulp.dest(scriptsPath));
   });

   // process all remaining files in scriptsPath root into main.js and main.min.js files
   var root = gulp.src(path.join(scriptsPath, '/*.js'))
        .pipe(concat('main.js'))
        .pipe(gulp.dest(scriptsPath))
        .pipe(uglify())
        .pipe(rename('main.min.js'))
        .pipe(gulp.dest(scriptsPath));

   return merge(tasks, root);
});
```

いくつかの注記

- `folders.map` - フォルダごとに関数を実行し、async ストリームを返す
- `merge` - ストリームを組み合わせ、すべてのストリームが発火し終わったときにのみ終了する
