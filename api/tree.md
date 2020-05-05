<!-- front-matter
id: tree
title: tree()
hide_title: true
sidebar_label: tree()
-->

# tree()

現在のタスク依存ツリーを取得します。まれに必要になる場合があります。

一般的に、`tree()` は gulp コンシューマーによって使用されませんが、gulpfile に定義されたタスクの依存グラフを CLI が表示できるように公開されています。

## 使用法

gulpfile の例
```js

const { series, parallel } = require('gulp');

function one(cb) {
  // body omitted
  cb();
}

function two(cb) {
  // body omitted
  cb();
}

function three(cb) {
  // body omitted
  cb();
}

const four = series(one, two);

const five = series(four,
  parallel(three, function(cb) {
    // Body omitted
    cb();
  })
);

module.exports = { one, two, three, four, five };
```

`tree()` の出力
```js
{
  label: 'Tasks',
  nodes: [ 'one', 'two', 'three', 'four', 'five' ]
}
```


`tree({ deep: true })` の出力
```js
{
  label: "Tasks",
  nodes: [
    {
      label: "one",
      type: "task",
      nodes: []
    },
    {
      label: "two",
      type: "task",
      nodes: []
    },
    {
      label: "three",
      type: "task",
      nodes: []
    },
    {
      label: "four",
      type: "task",
      nodes: [
        {
          label: "<series>",
          type: "function",
          branch: true,
          nodes: [
            {
              label: "one",
              type: "function",
              nodes: []
            },
            {
              label: "two",
              type: "function",
              nodes: []
            }
          ]
        }
      ]
    },
    {
      label: "five",
      type: "task",
      nodes: [
        {
          label: "<series>",
          type: "function",
          branch: true,
          nodes: [
            {
              label: "<series>",
              type: "function",
              branch: true,
              nodes: [
                {
                  label: "one",
                  type: "function",
                  nodes: []
                },
                {
                  label: "two",
                  type: "function",
                  nodes: []
                }
              ]
            },
            {
              label: "<parallel>",
              type: "function",
              branch: true,
              nodes: [
                {
                  label: "three",
                  type: "function",
                  nodes: []
                },
                {
                  label: "<anonymous>",
                  type: "function",
                  nodes: []
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

## シグネチャ

```js
tree([options])
```

### パラメーター

| パラメーター | 型 | 備考 |
|:--------------:|------:|--------|
| options | object | 詳細は以下の[オプション][options-section]にあります。 |

### 返り値

登録されたタスクのツリーを詳述するオブジェクト。（[archy][archy-external]と互換性がある）`'label'` と `'nodes'` プロパティをもつネストされたオブジェクトを含みます。

各オブジェクトは、ノードが `task` または `function` であるか判定するために使用できる `type` プロパティをもつことができます。

各オブジェクトは `branch` プロパティをもつことができ、`true` の場合、ノードが `series()` または `parallel()` を使用して作成されたことを示します。

### オプション

| 名前 | 型 | 初期値 | 備考 |
|:-------:|:-------:|------------|--------|
| deep | boolean | false | true の場合、全体のツリーが返ります。false の場合、トップレベルのタスクだけが返ります。 |

[options-section]: #options
[archy-external]: https://www.npmjs.com/package/archy
