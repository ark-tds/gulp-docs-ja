<!-- front-matter
id: quick-start
title: Quick Start
hide_title: true
sidebar_label: Quick Start
-->

# クイックスタート

過去に gulp をグローバルにインストールしていた場合、これらの手順に従う前に `npm rm --global gulp` を実行してください。詳細は、この [Sip][sip-article] を読んでください。

## node と npm、npx を確認する
```sh
node --version
```
![Output: v8.11.1][img-node-version-command]
```sh
npm --version
```
![Output: 5.6.0][img-npm-version-command]
```sh
npx --version
```
![Output: 9.7.1][img-npx-version-command]

インストールされていない場合、[こちら][node-install]の手順に従ってください。

## gulp コマンドラインユーティリティをインストールする
```sh
npm install --global gulp-cli
```


## プロジェクトディレクトリを作成し、そこへ移動する
```sh
npx mkdirp my-project
```
```sh
cd my-project
```

## プロジェクトディレクトリに package.json ファイルを作成する
```sh
npm init
```

これにより、プロジェクトに名前、バージョン、説明などを付けるガイドが行われます。

## devDependencies に gulp パッケージをインストールする
```sh
npm install --save-dev gulp
```

## gulp のバージョンを確認する

```sh
gulp --version
```

出力が下のスクリーンショットと一致するか確認してください。そうしなければ、このガイドの手順をはじめから始める必要があるかもしれません。

![Output: CLI version 2.0.1 & Local version 4.0.0][img-gulp-version-command]

## gulpfile を作成する
テキストエディタを用いて、プロジェクトルートに gulpfile.js という名前のファイルを作成して、次の内容を記述してください。
```js
function defaultTask(cb) {
  // place code for your default task here
  cb();
}

exports.default = defaultTask
```

## テストする
プロジェクトディレクトリで gulp コマンドを実行してください。
```sh
gulp
```
複数のタスクを実行するためには、`gulp <task> <othertask>` を使用できます。

## 結果
デフォルトタスクが実行され、何も起きません。
![Output: Starting default & Finished default][img-gulp-command]

[sip-article]: https://medium.com/gulpjs/gulp-sips-command-line-interface-e53411d4467
[node-install]: https://nodejs.org/en/
[img-node-version-command]: https://gulpjs.com/img/docs-node-version-command.png
[img-npm-version-command]: https://gulpjs.com/img/docs-npm-version-command.png
[img-npx-version-command]: https://gulpjs.com/img/docs-npx-version-command.png
[img-gulp-version-command]: https://gulpjs.com/img/docs-gulp-version-command.png
[img-gulp-command]: https://gulpjs.com/img/docs-gulp-command.png
