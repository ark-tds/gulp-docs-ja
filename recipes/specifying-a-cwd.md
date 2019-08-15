# 新しい cwd（current working directory）の指定

これは、次のようなネストしたディレクトリ構造をもつプロジェクトで便利です。

```
/project
  /layer1
  /layer2
```

gulp CLI オプション `--cwd` を使用できます。

`project/` ディレクトリから

```sh
gulp --cwd layer1
```

特定の glob に対してのみ cwd を指定する必要がある場合、[glob-stream](https://github.com/gulpjs/glob-stream) 上で `cwd` オプションを使用できます。

```js
gulp.src('./some/dir/**/*.js', { cwd: 'public' });
```
