# FAQ

## なぜ gulp？ なぜ ____ ではない？

どうして gulp なのか、要約は [gulp の紹介スライドショー] を参照してください。

## "gulp" ですか "Gulp" ですか？

gulp は常に小文字です。gulp が大文字になる唯一の例外は gulp のロゴです。

## どこで gulp プラグインの一覧を見つけることができますか？

gulp プラグインは `gulpplugin` キーワードを含みます。[gulp plugins を検索する][search-gulp-plugins]または[すべてのプラグインを見てください][npm plugin search]。

## gulp プラグインを書きたいのですが、どのように始めればいいですか？

始めるために、[gulp プラグインの書き方] wiki ページでガイドラインと例を参照してください。

## 私のプラグインは ____ を行うが、やりすぎですか？

たぶん。次のことを自問してください。

1. 私のプラグインは他のプラグインが行う必要があるかもしれないことを行っているか？
  - もしそうならば、機能のその部分は別のプラグインであるべきです。[npm にすでに存在しているか確認してください][npm plugin search]。
1. 私のプラグインは設定オプションに基づいた2つの、まったく異なることを行っているか？
  - もしそうならば、2つの別のプラグインとしてリリースすると、コミュニティに役立つかもしれません。
  - もし2つのタスクが異なるなが、密接に関連しているならば、たぶんOKです

## プラグイン出力で改行はどのようにして表現すべきですか？How should newlines be represented in plugin output?

オペレーションシステム間の差分の問題を防ぐために、常に `\n` を使用してください。

## どこで gulp の最新情報を得ることができますか？

gulp の最新情報は次の twitter で見つけることができます。

- [@wearefractal](https://twitter.com/wearefractal)
- [@eschoff](https://twitter.com/eschoff)
- [@gulpjs](https://twitter.com/gulpjs)

## gulp のチャットチャンネルはありますか?

はい、[Gitter](https://gitter.im/gulpjs/gulp) でチャットしてください。

[gulp プラグインの書き方]: writing-a-plugin/README.md
[gulp の紹介スライドショー]: https://slid.es/contra/gulp
[Freenode]: https://freenode.net/
[search-gulp-plugins]: https://gulpjs.com/plugins/
[npm plugin search]: https://npmjs.org/browse/keyword/gulpplugin
