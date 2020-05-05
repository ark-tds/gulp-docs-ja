<!-- front-matter
id: vinyl-isvinyl
title: Vinyl.isVinyl()
hide_title: true
sidebar_label: Vinyl.isVinyl()
-->

# Vinyl.isVinyl()

オブジェクトが Vinyl インスタンスか判定します。`instanceof` の代わりにこのメソッドを使用してください。

**注意**: このメソッドは、Vinyl の古いバージョンの一部が公開していない内部のプロパティを使用しており、古いバージョンを使用している場合、 逆に false となります。

## 使用法

```js
const Vinyl = require('vinyl');

const file = new Vinyl();
const notAFile = {};

Vinyl.isVinyl(file) === true;
Vinyl.isVinyl(notAFile) === false;
```

## シグネチャ

```js
Vinyl.isVinyl(file);
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:------:|-------|
| file | object | 確認するためのオブジェクト。 |

### 返り値

`file` オブジェクトが Vinyl インスタンスの場合、true。
