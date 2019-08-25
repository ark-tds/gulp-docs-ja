<!-- front-matter
id: registry
title: registry()
hide_title: true
sidebar_label: registry()
-->

# registry()

カスタムレジストリをタスクシステムにプラグインして、共有タスクまたは拡張機能を提供できます。

**注意：** `task()` で登録されたタスクのみがカスタムレジストリに提供されます。`series()` または `parallel()` に直接渡されたタスク関数は提供されません。レジストリの動作をカスタマイズする必要がある場合、タスクを文字列参照で合成してください。

新しいレジストリを割り当てる場合、現在のレジストリの各タスクが転送され、現在のレジストリは新しいものに置き換えられます。これにより、複数のカスタムレジストリを順番に追加できます。

詳細は[カスタムレジストリの作成][creating-custom-registries]を参照してください。

## 使用法

```js
const { registry, task, series } = require('gulp');
const FwdRef = require('undertaker-forward-reference');

registry(FwdRef());

task('default', series('forward-ref'));

task('forward-ref', function(cb) {
  // body omitted
  cb();
});
```

## シグネチャ

```js
registry([registryInstance])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|:-----:|--------|
| registryInstance | object | カスタムレジストリのインスタンス。クラスではない。 |

### 返り値

`registryInstance` が渡された場合、何も返りません。引数が渡されない場合、現在のレジストリインスタンスを返します。

### エラー

（インスタンスの代わりに）コンストラクタが `registryInstance` として渡された場合、"Custom registries must be instantiated, but it looks like you passed a constructor" というメッセージとともにエラーが投げられます。

`get` メソッドがないレジストリが `registryInstance` として渡された場合、"Custom registry must have `get` function" というメッセージとともにエラーが投げられます。

`set` メソッドがないレジストリが `registryInstance` として渡された場合、"Custom registry must have `set` function" というメッセージとともにエラーが投げられます。

`init` メソッドがないレジストリが `registryInstance` として渡された場合、"Custom registry must have `init` function" というメッセージとともにエラーが投げられます。

`tasks` メソッドがないレジストリが `registryInstance` として渡された場合、"Custom registry must have `tasks` function" というメッセージとともにエラーが投げられます。

[creating-custom-registries]: ../documentation-missing.md
