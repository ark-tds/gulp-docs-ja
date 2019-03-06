<!-- front-matter
id: explaining-globs
title: Explaining Globs
hide_title: true
sidebar_label: Explaining Globs
-->

# glob の説明

glob はファイルパスを照合するために用いられるリテラル文字列かつ/またはワイルドカード文字です。globbing とは、1つ以上の glob を用いてファイルシステム上のファイルを見つけることです。

`src()` メソッドは、パイプラインがどのファイルを操作するか決定するために、単一の glob 文字列または glob の配列を要求します。glob から少なくとも1つは一致しなければならず、そうでなければ `src()` はエラーを起こします。glob の配列が使用されたとき、それらは配列順で照合されます。これは、特にネガティブな glob のために役立ちます。

## セグメントとセパレーター

セグメントとはセパレーターの間にあるすべてです。glob 内のセパレーターはオペレーティングシステムにかかわらず、パスのセパレーターが `\\` である Windows でさえ、常に `/` 文字です。glob 内で、`\\` はエスケープ文字として予約されています。

ここでは、* がエスケープされ、ワイルドカード文字の代わりにリテラルとして扱われます。
```js
'glob_with_uncommon_\\*_character.js'
```

glob を作成するために、`path.join` のような Node の `path` メソッドの使用しないでください。Windows で、Node はセパレーターとして `\\` を使用するため、無効な glob を生成します。同じ理由から、`__dirname` グローバルや `__filename` グローバル、`process.cwd()` も避けてください。

```js
const invalidGlob = path.join(__dirname, 'src/*.js');
```

## 特殊文字：*（シングルスター）

単一のセグメント内の0を含む任意の数の文字と一致します。1つのディレクトリ内のファイルを globbing するのに役立ちます。

この glob は `index.js` のようなファイルに一致し、`scripts/index.js` や `scripts/nested/index.js` のようなファイルには一致しません。
```js
'*.js'
```

## 特殊文字：**（ダブルスター）

セグメントを横断した0を含む任意の数の文字と一致します。ネストしたディレクトリ内のファイルを globbing するのに役立ちます。不必要に大きなディレクトリを一致させないように、ダブルスター glob を適切に制限してください。

ここで、glob は `scripts/` ディレクトリに適切に制限されています。この glob は`scripts/index.js` や `scripts/nested/index.js`、`scripts/nested/twice/index.js` のようなファイルに一致します。

```js
'scripts/**/*.js'
```

<small>前述の例で、`scripts/` のプレフィックスがなければ、`node_modules` 内のすべての依存関係や他のディレクトリまでも一致してしまいます。</small>

## 特殊文字：!（ネガティブ）

glob は配列順に一致するので、配列内でネガティブ glob は少なくとも1つの非ネガティブ glob の後に続かなければなりません。まず非ネガティブ glob に一致するものを見つけ、そしてネガティブ glob はそれらの結果の一部を削除します。これらはリテラル文字だけを含むとき最もパフォーマンスがいいです。
```js
['script/**/*.js', '!scripts/vendor/']
```

任意の非ネガティブ glob がネガティブ glob に続いた場合、非ネガティブ glob に一致するものは削除されません。

```js
['script/**/*.js', '!scripts/vendor/', 'scripts/vendor/react.js']
```

ダブルスター glob を制限するための代用としてネガティブ glob を使用することができます。

```js
['**/*.js', '!node_modules/']
```

<small>前述の例で、ネガティブ glob が `!node_modules/**/*.js` である場合、一致したすべてのものをネガティブ glob と比較しなければならず、非常に遅くなります。</small>

## glob の重複

同じファイルに意図して（または、意図せず）一致する2つ以上の glob は重複として見なされます。重複する glob が単一の `src()` 内で使用されるとき、gulp はその重複を削除するように最善を尽くしますが、異なる `src()` 呼び出しにまたがって重複の削除を試みることはしません。

## 高度なリソース

gulp で glog を扱うために必要なことのほとんどはここでカバーされています。より深く知りたい場合、ここにいくつかのリソースがあります。

* [Micromatch ドキュメント][micromatch-docs]
* [node-glob の glob 入門書][glob-primer-docs]
* [Beginの Globbing ドキュメント][begin-globbing-docs]
* [Wikipedia の glob ページ][wikipedia-glob]

[micromatch-docs]: https://github.com/micromatch/micromatch
[glob-primer-docs]: https://github.com/isaacs/node-glob#glob-primer
[begin-globbing-docs]: https://github.com/begin/globbing#what-is-globbing
[wikipedia-glob]: https://en.wikipedia.org/wiki/Glob_(programming)
