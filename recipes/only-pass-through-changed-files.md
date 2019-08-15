# 変更ファイルのみ渡す

デフォルトでは、ファイルは実行ごとにパイプチェーン全体に渡されます。[gulp-changed](https://github.com/sindresorhus/gulp-changed) を使用すると、変更ファイルだけが渡されます。これにより連続的な実行を大幅に高速化できます。


```js
// npm install --save-dev gulp gulp-changed gulp-jscs gulp-uglify

var gulp = require('gulp');
var changed = require('gulp-changed');
var jscs = require('gulp-jscs');
var uglify = require('gulp-uglify');

// we define some constants here so they can be reused
var SRC = 'src/*.js';
var DEST = 'dist';

gulp.task('default', function() {
	return gulp.src(SRC)
		// the `changed` task needs to know the destination directory
		// upfront to be able to figure out which files changed
		.pipe(changed(DEST))
		// only files that has changed will pass through here
		.pipe(jscs())
		.pipe(uglify())
		.pipe(gulp.dest(DEST));
});
```
