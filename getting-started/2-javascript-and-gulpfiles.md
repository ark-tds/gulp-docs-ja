<!-- front-matter
id: javascript-and-gulpfiles
title: JavaScript and Gulpfiles
hide_title: true
sidebar_label: JavaScript and Gulpfiles
-->

# JavaScript と Gulpfiles

gulp を使うと、既存の JavaScript の知識を用いて gulpfile を書いたり、gulpfile を利用した経験をもとにプレーンな JavaScript を書くことができます。ファイルシステムとコマンドラインの操作を簡単にするためのユーティリティがいくつか用意されていますが、それ以外のすべてはピュアな JavaScript です。

## gulpfile の説明

gulpfile はプロジェクトディレクトリで `gulpfile.js`（または、大文字で表記された `Gulpfile.js`）と名付けられたファイルで、`gulp` コマンドを実行すると自動的に読み込まれます。このファイルの中に、`src()` または `dest()`、`series()`、`parallel()` などの gulp API がよくありますが、バニラ JavaScript や Node モジュールを使用できます。エクスポートされた関数は gulp のタスクシステムに登録されます。

## トランスパイル

TypeScript や Babel のようなトランスパイルを必要とする言語を用いて gulpfile を書くには、その言語を示すように `gulpfile.js` の拡張子を変更し、トランスパイラモジュールをインストールします。

* TypeScript の場合、`gulpfile.ts` へ名前を変更し、[ts-node][ts-node-module] モジュールをインストールします。
* Babel の場合、`gulpfile.babel.js` へ名前を変更し、[@babel/register][babel-register-module] モジュールをインストールします。

__node のほとんどの新しいバージョンは、`import`/`export` シンタックスを除いて、TypeScript や Babel が提供しているほとんどの機能をサポートしています。このシンタックスを必要としているときだけ、`gulpfile.esm.js` に名前を変更して [esm][esm-module] モジュールをインストールしてください。__

このトピックとサポートされた拡張子の一覧についての詳細は、[gulpfile トランスパイル][gulpfile-transpilation-advanced] のドキュメントを参照してください。

## gulpfile の分割

多くのユーザーは gulpfile にすべてのロジックを追加することから初めます。gulpfile が大きくなりすぎた場合、別々のファイルにリファクタリングすることができます。

各タスクを自身のファイルに分割でき、それらを組み合わせるために gulpfile へインポートできます。これによって、タスクを整理できるだけでなく、各タスクを個別にテストしたり、条件にもとづいて構成を変更したりできます。

Node のモジュール解決により、`gulpfile.js` ファイルを、`gulpfile.js` として扱われる `index.js` ファイルを含む `gulpfile.js` という名前のディレクトリに置き換えることができます。このディレクトリはタスク用の個々のモジュールを含めることができます。トランスパイラを使用している場合、このディレクトリとファイルを適宜命名してください。

[gulpfile-transpilation-advanced]: ../documentation-missing.md
[ts-node-module]: https://www.npmjs.com/package/ts-node
[babel-register-module]: https://www.npmjs.com/package/@babel/register
[esm-module]: https://www.npmjs.com/package/esm
