# Git付属のWeb UIを使う：Gitwebとgit instaweb

[全体の案内へ戻る](../README.md) · [コマンドの実習](../git/README.md) · [ローカルのベア](../bare/README.md)

Gitには、リポジトリの履歴やファイルをブラウザーで見る **Gitweb** があります。`git instaweb` は、GitwebとHTTPサーバーを使って、手元のリポジトリを手軽に閲覧するためのコマンドです。

Git付属の機能ですが、Gitの実行ファイルだけで動くわけではありません。Gitweb、Perlのモジュール、対応するHTTPサーバーが必要で、OSや配布パッケージによって別途導入する場合があります。

公式資料の確認日：2026年11月7日。この章はLinuxで、自分のPCからだけ閲覧する使い方を扱います。

## 何ができるか

| 機能 | Gitwebでの操作 |
| --- | --- |
| 履歴を調べる | コミット一覧やブランチの履歴を見る |
| 変更を比べる | コミットの差分、ファイルの変更履歴を見る |
| ファイルを読む | 指定したコミットのディレクトリ・ファイルを開く |
| ブランチ・タグを調べる | 参照先のコミットへ移動する |

編集・コミット・pushは、[Gitコマンド](../git/README.md)や [TortoiseGit](../tortoisegit/README.md)で行います。利用者管理やMerge Requestなども含むサービスを用意したい場合は、[GitLab](../gitlab/README.md)を参照してください。

## 1. 必要なものを確認する

```sh
git --version
git instaweb -h
command -v lighttpd
perl -MCGI -e 'print "$CGI::VERSION\n"'
```

この章ではHTTPサーバーに `lighttpd` を使います。`git instaweb -h` でヘルプが出ても、Gitweb本体やHTTPサーバーが揃っているとは限りません。

Debian・Ubuntuの新しい検証用環境で不足分を導入する例です。

```sh
sudo apt update
sudo apt install --no-install-recommends git gitweb lighttpd
```

**パッケージ導入と、次のローカル限定起動は別です。** `lighttpd` の導入ではシステムのWebサービスが起動する場合があり、既存のApacheなどがあるとGitwebの配信設定も追加される場合があります。既存Webサーバーのある環境では、管理者が配信設定と待ち受けを確認してください。

新規の検証環境で、常駐のlighttpdサービスを使わず、この実習だけで起動するなら、システムサービスは停止して自動起動を無効にできます。既存サービスを運用している環境では、そのまま実行しません。

```sh
sudo systemctl disable --now lighttpd
```

`git instaweb` は自分の権限で別のHTTPサーバーを起動するので、この常駐サービスが動いている必要はありません。WindowsやmacOSでは同じパッケージ構成とは限らず、Gitwebと対応サーバーの有無を先に確認します。

参考：[git instawebの対応サーバー](https://git-scm.com/docs/git-instaweb)、[Debianのgitwebパッケージ](https://packages.debian.org/trixie/gitweb)

## 2. 手元のリポジトリを開く

コミットがある練習用リポジトリで実行します。`/path/to/repository` は、実際の作業フォルダーへ置き換えます。

```sh
cd /path/to/repository
git instaweb --local --httpd=lighttpd --port=1234
```

ブラウザーが開き、Gitwebの画面が表示されます。自動で開かなかった場合は、起動結果を確認してから [http://127.0.0.1:1234/](http://127.0.0.1:1234/) を開きます。

| 指定 | 意味 |
| --- | --- |
| `--local` | 待ち受けを `127.0.0.1` に限定する。他のPCから直接接続する設定にしない |
| `--httpd=lighttpd` | 起動するHTTPサーバーを指定する |
| `--port=1234` | 閲覧するポートを指定する。使用中なら別の空きポートを選ぶ |

この操作は履歴を書き換えませんが、通常のリポジトリでは `.git` 内に閲覧用の設定・一時ファイル・ログなどを作ります。起動したサーバーは、あとで同じリポジトリから停止します。

## 3. 画面の使い方

リポジトリ一覧が出た場合は、見たいリポジトリのリンクを開きます。

| 画面の項目 | 見るもの |
| --- | --- |
| `summary` | 最近の変更と概要 |
| `shortlog` / `log` | コミットの履歴 |
| `commit` | コミットのメッセージ・名義・変更対象 |
| `commitdiff` | そのコミットで何が変わったか |
| `tree` | 選んだ版のディレクトリとファイル |
| `blob` | ファイルの内容 |
| `heads` / `tags` | ブランチ・タグの参照先 |

たとえば、`summary` で最近のコミットを選び、`commitdiff` で差分を見てから、`tree` と `blob` でその版のファイルを読みます。項目や追加機能の有無は、Gitwebの設定によって変わります。

表示するのは、Gitに記録されたコミットや参照です。エディターで編集中の未コミット内容を、そのまま表示する画面ではありません。新しいコミットを確認する場合は、コミット後にブラウザーを再読み込みします。リモートの変更も、必要に応じて別途fetchなどで取得します。

参考：[Gitwebの機能と画面](https://git-scm.com/docs/gitweb)

## 4. 見終わったら停止する

起動したのと同じリポジトリの中で実行します。

```sh
git instaweb --stop
```

ブラウザーのタブを閉じるだけでは、HTTPサーバーの停止にはなりません。停止後は、同じURLを再読み込みして接続できなくなったことを確認します。

再び使う場合は、起動時と同じコマンドを実行します。

```sh
git instaweb --local --httpd=lighttpd --port=1234
```

### start・restartを使うときの注意

`--start` ならブラウザーを開かず起動でき、`--restart` は再起動に使えます。ただし、生成済みのHTTP設定を再利用する実装では、これらに別のポートや `--local` を渡しても、その設定が反映されるとは限りません。

待ち受けやポートを変える場合は、まず停止し、上のように `--start` / `--restart` を付けない通常のコマンドで設定を作り直します。`--local` を明示し、実際の待ち受けも確認してください。

参考：[起動と停止](https://git-scm.com/docs/git-instaweb)、[Gitのinstaweb実装](https://github.com/git/git/blob/master/git-instaweb.sh)

## ベアリポジトリでも使える

作業用クローンだけでなく、ベアリポジトリの中でも、同じ起動・停止の操作ができます。ファイルを作業ツリーへ展開せず、保存済みの履歴を閲覧します。

ただし、`git instaweb` が生成する探索ルートによっては、隣にあるベアリポジトリも一覧へ出ることがあります。選んだ1個だけが表示対象だと決めつけず、実際の一覧と表示範囲を確認します。

画面には、過去のファイル・コミット名義・メール・リポジトリ所有者などが表示されます。公開リポジトリの現在のファイルだけを表示する画面ではないため、スクリーンショットや画面共有にも気を配りましょう。

ローカルでの閲覧を、LANやインターネットへの常設公開へ変える場合は、公開対象の選択、認証、HTTPS、Webサーバーの設定と保守を別途行います。この章の `--local` を外すだけの手順にはしません。

参考：[Gitwebの表示対象](https://git-scm.com/docs/gitweb)、[Gitwebの設定](https://git-scm.com/docs/gitweb.conf)
