<!-- front-matter
id: vinyl-iscustomprop
title: Vinyl.isCustomProp()
hide_title: true
sidebar_label: Vinyl.isCustomProp()
-->

# Vinyl.isCustomProp()

プロパティが Vinyl によって内部的に管理されているか判定します。コンストラクタ内で値を設定するとき、または `clone()` インスタンスメソッドでプロパティをコピーするときに、Vinyl によって使用されます。

このメソッドは、Vinyl クラスを拡張するときに役立ちます。詳細は以下の [Vinyl の拡張][extending-vinyl-section]にあります。

## 使用法

```js
const Vinyl = require('vinyl');

Vinyl.isCustomProp('sourceMap') === true;
Vinyl.isCustomProp('path') === false;
```

## シグネチャ

```js
Vinyl.isCustomProp(property)
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| property | string | 確認するためのプロパティ名。 |

### 返り値

プロパティが内部的に管理されている場合、true。

## Vinyl の拡張

カスタムプロパティが内部的に管理されている場合、static な `isCustomProp` メソッドを拡張して、カスタムプロパティの1つがクエリされたときに false を返す必要があります。

```js
const Vinyl = require('vinyl');

const builtInProps = ['foo', '_foo'];

class SuperFile extends Vinyl {
  constructor(options) {
    super(options);
    this._foo = 'example internal read-only value';
  }

  get foo() {
    return this._foo;
  }

  static isCustomProp(name) {
    return super.isCustomProp(name) && builtInProps.indexOf(name) === -1;
  }
}
```

上記の例では、複製または `new SuperFile(options)` への `options` に渡す場合、`foo` と `_foo` は新しいオブジェクトに割り当てられません。

カスタムプロパティまたはロジックで複製中に特別な処理が必要な場合、Vinyl の拡張中に `clone` メソッドをオーバーライドしてください。

[extending-vinyl-section]: #extending-vinyl
