# cron ジョブを介して gulp タスクを実行する

現在の "[crontab](https://en.wikipedia.org/wiki/Cron)" ファイルを編集するために、`gulp` を実行する権限をもつユーザーでログインして、次を実行します。

    crontab -e

通常、cron ジョブ内では絶対パスを使用して任意のバイナリを実行するため、1分ごとに `gulp build` を実行するための最初のアプローチは次のようになります。

    * * * * * cd /your/dir/to/run/in && /usr/local/bin/gulp build

しかしながら、cron ログで次のエラーが表示されることもあります。

> `/usr/bin/env: node: No such file or directory`

これを修正するために、node バイナリの実際のパスを指すために、`/usr/bin` 内に[シンボリックリンク](https://en.wikipedia.org/wiki/Ln_\(Unix\))を追加する必要があります。

**sudo** ユーザーとしてログインしていることを確認し、ターミナルに次のコマンドをペーストしてください。

    sudo ln -s $(which node) /usr/bin/node

このリンクが確立されると、cron タスクは正常に実行されるはずです。
