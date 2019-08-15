# Globbing しながらディレクトリ構造を維持する

ディレクトリからいくつかのファイル/フォルダを読み、それらの相対パスを維持したい場合、`gulp.src()` の第2引数として `{base: '.'}` を渡す必要があります。


例えば、次のようなディレクトリ構造で、

![Dev setup](https://cloud.githubusercontent.com/assets/2562992/3178498/bedf75b4-ec1a-11e3-8a71-a150ad94b450.png)

次のようないくつかのファイルだけを読みたい場合。

```js
[ 'index.html',
 'css/**',
 'js/**',
 'lib/**',
 'images/**',
 'plugin/**'
 ]
```

この場合、Gulp は（_例えば_）`css` フォルダのすべてのサブフォルダを読み、それらをルートフォルダに相対的に配置し、それらは `css` のサブフォルダではなくなります。globbing 後の出力は次のようになります。

![Zipped-Unzipped](https://cloud.githubusercontent.com/assets/2562992/3178614/27208c52-ec1c-11e3-852e-8bbb8e420c7f.png)

その構造を維持したい場合、次のように `gulp.src()` へ `{base: '.'}` を渡す必要があります。

```js
gulp.task('task', function () {
   return gulp.src(['index.html',
             'css/**',
             'js/**',
             'lib/**',
             'images/**',
             'plugin/**'
             ], {base: '.'})
       .pipe(operation1())
       .pipe(operation2());
});
```
そうすると、`operation1()` への入力は次のようなフォルダ構造となります。

![with-base](https://cloud.githubusercontent.com/assets/2562992/3178607/053d6722-ec1c-11e3-9ba8-7ce39e1a480e.png)
