<!-- front-matter
id: concepts
title: API Concepts
hide_title: true
sidebar_label: Concepts
-->

# コンセプト

次のコンセプトは、API ドキュメントを理解するための前提条件です。これらは全体にわたって参照されうるので、詳細な説明のためにこのページを参照してください。

ここが初めての場合、[Getting Started ガイド][quick-start-docs]から始めてください。

## Vinyl

Vinyl とは、ファイルを記述するメタデータオブジェクトです。Vinyl インスタンスの主なプロパティは、`path` と `contents` です。これは、ファイルシステム上のファイルのコア部分です。Vinyl オブジェクトを使用すると、ローカルファイルシステムまたはリモートストレージオプション上の多くのリソースからファイルを記述することができます。

## Vinyl アダプター

Vinyl はファイルを記述する方法を提供しますが、これらのファイルにアクセスする方法が必要です。各ファイルソースには、Vinyl アダプターを使用してアクセスします。

アダプターは次を提供します。
* シグネチャ `src(globs, [options])` をもつメソッド。Vinyl オブジェクトを生成するストリームを返します。
* シグネチャ `dest(folder, [options])` をもつメソッド。Vinyl オブジェクトを消費するストリームを返します。
* `vinyl-fs` が提供する `symlink` メソッドのような入力/出力メディアに特化した追加メソッド。Vinyl オブジェクトを生成かつ/または消費するストリームを常に返すべきです。

## タスク

各 gulp タスクはエラーファーストコールバックを受け入れるか、ストリーム、promise、イベントエミッター、子プロセス、observable を返すかのどちらかを行う非同期 JavaScript 関数です。いくつかのプラットフォームの制限により、同期タスクはサポートされていません。

より詳細な説明については、[タスクの作成][creating-tasks-doc]を参照してください。

## Globs

glob とは、ファイルパスの一致に使用される、`*` や `**`、`!` のようなリテラルかつ/またはワイルドカード文字の文字列です。Globbing とは、1つ以上の glob を用いてファイルシステム上のファイルを見つける行為です。

glob の経験がない場合、[Globs の説明][explaining-globs-docs]を参照してください。

## Glob base

glob base（glob parent とも呼ばれる）とは、glob 文字列内の特殊文字の前のパスセグメントです。したがって、`/src/js/**.js` の glob base は `/src/js/` です。glob に一致するすべてのパスは、glob base を共有することが保証されています。そのパスセグメントは可変にできません。

`src()` によって生成された Vinyl インスタンスは、それらの `base` プロパティとして設定された glob base で構築されます。`dest()` でファイルシステムに書き込まれると、ディレクトリ構造を保持するために出力パスから `base` が削除されます。

詳細については、[glob-parent][glob-parent-external] リポジトリを参照してください。

## ファイルシステム統計

ファイルのメタデータは、Node の [`fs.Stats`][fs-stats-external] のインスタンスとして提供されます。Vinyl インスタンスの `stat` プロパティとして利用可能で、Vinyl オブジェクトがディレクトリまたはシンボリックリンクを表すかどうかを決定するために内部的に使用されます。ファイルシステムに書き出されると、権限と時間の値が Vinyl オブジェクトの `stat` プロパティから同期されます。

## ファイルシステムモード

ファイルシステムモードは、ファイルに対してどの権限が存在するか決定します。ファイルシステム上のほとんどのファイルとディレクトリはかなり寛容なモードをもち、gulp がユーザーに代わってファイルの読み取り/書き込み/更新を行うことができます。デフォルトでは、gulp は実行中のプロセスと同じ権限をもつファイルを作成しますが、`src()` や `dest()` などのオプションを通してモードを設定できます。権限（EPERM）の問題が発生した場合、ファイルのモードを確認してください。

## モジュール

Gulpは多くの小さなモジュールで構成されており、それらがまとまって連携して動作します。小さなモジュール内で [semver][semver-external] を利用することにより、gulp の新しいバージョンを公開せずにバグ修正と機能をリリースできます。しばしば、メインリポジトリで進捗がないとき、これらのモジュールのいずれかで作業が行われています。

問題が発生した場合、`npm update` コマンドを使用して現在のモジュールを更新されていることを確認してください。問題が解決しない場合、個々のプロジェクトリポジトリでイシューを開いてください。

* [undertaker][undertaker-external] - タスク登録システム
* [vinyl][vinyl-external] - 仮想的なファイルオブジェクト
* [vinyl-fs][vinyl-fs-external] - ローカルファイルシステムへの Vinyl アダプター
* [glob-watcher][glob-watcher-external] - ファイルウォッチャー
* [bach][bach-external] - `series()` と `parallel()` を使用したタスクオーケストレーション
* [last-run][last-run-external] - タスクの最終実行時間を追跡する
* [vinyl-sourcemap][vinyl-sourcemap-external] - ビルトインソースマップサポート
* [gulp-cli][gulp-cli-external] - gulp と対話するためのコマンドラインインターフェース


[quick-start-docs]: ../getting-started/1-quick-start.md
[creating-tasks-doc]: ../getting-started/3-creating-tasks.md
[explaining-globs-docs]: ../getting-started/6-explaining-globs.md
[undertaker-external]: https://github.com/gulpjs/undertaker
[vinyl-external]: https://github.com/gulpjs/vinyl
[vinyl-fs-external]: https://github.com/gulpjs/vinyl-fs
[glob-watcher-external]: https://github.com/gulpjs/glob-watcher
[bach-external]: https://github.com/gulpjs/bach
[last-run-external]: https://github.com/gulpjs/last-run
[vinyl-sourcemap-external]: https://github.com/gulpjs/vinyl-sourcemap
[gulp-cli-external]: https://github.com/gulpjs/gulp-cli
[semver-external]: https://semver.org
[fs-stats-external]: https://nodejs.org/api/fs.html#fs_class_fs_stats
[glob-parent-external]: https://github.com/es128/glob-parent
