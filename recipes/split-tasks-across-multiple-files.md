# 複数のファイルにタスクを分割する

`gulpfile.js` が大きくなり始めた場合、[gulp-hub](https://github.com/frankwallis/gulp-hub/tree/4.0) モジュールを[カスタムレジストリ](https://github.com/phated/undertaker#registryregistryinstance)として使用することによってタスクを個別のファイルに分割できます。

次のファイル構造を想像してください。

```
gulpfile.js
tasks/
├── dev.js
├── release.js
└── test.js
```

`gulp-hub` モジュールをインストールします。

```sh
npm install --save-dev gulp gulp-hub
```

次の行を `gulpfile.js` に追加します。

```js
'use strict';

var gulp = require('gulp');
var HubRegistry = require('gulp-hub');

/* load some files into the registry */
var hub = new HubRegistry(['tasks/*.js']);

/* tell gulp to use the tasks just loaded */
gulp.registry(hub);
```

このレシピは https://github.com/frankwallis/gulp-hub/tree/4.0/examples/recipe にもあります。
