# GitLabを使う

[全体の案内へ戻る](../README.md) · [GitLab.comの登録と初期設定](account.md) · [自前サーバーのセットアップ](self-managed.md)

GitLabは、Gitリポジトリの保管に、Issue、変更レビュー、CI/CDなどを組み合わせたサービス・ソフトウェアです。「公式のホスティングサービスを使う方法」と「自分でサーバーを運用する方法」があります。

## どちらを使うか

| 比較するもの | GitLab.com | GitLab Self-Managed |
| --- | --- | --- |
| サービスの運用者 | GitLab | 自分や所属組織 |
| 接続先 | `gitlab.com` | 管理者が用意したホスト名 |
| 始めるとき | アカウントを登録する | サーバーを構築してから利用者を用意する |
| 更新・バックアップ | サービス側の運用方針を確認する | サーバー管理者が計画して実施する |
| この教材の手順 | [登録と初期設定](account.md) | [Ubuntuでの構築](self-managed.md) |

どちらもGitLabの提供形態です。GitLab.comのアカウントを作っても、自前サーバーへ同じアカウントで自動的にログインできるわけではありません。プロジェクト、SSH鍵、トークンも、接続先ごとに確認・登録します。

[前章のSSHベアサーバー](../server/README.md)は、Gitの送受信用の構成です。GitLabはWeb画面やデータベースなども動かすため、必要な資源・管理方法が異なります。

## 用語の対応

| 言葉 | この章での意味 |
| --- | --- |
| プロジェクト | GitリポジトリとIssue・設定などをまとめた単位 |
| 名前空間・namespace | ユーザーやグループの所有先。URLの `NAMESPACE` に相当する |
| グループ | 複数のプロジェクトとメンバー権限をまとめる単位 |
| Merge Request | ブランチの変更をレビューして取り込む提案。GitHubのPull Requestに相当する |

グループ配下では `GROUP/SUBGROUP/PROJECT` のように階層が増えることもあります。URLは手作業で推測せず、対象プロジェクトの画面からコピーします。

## 1. 最初のPrivateプロジェクトを作る

アカウントと接続の準備が済んだら、利用するGitLabへログインして進めます。自前版は管理者の案内と権限設定にも従います。

1. 右上の作成メニューから `New project/repository` を開きます。
2. `Create blank project` を選びます。
3. プロジェクト名とURLのパス、作成先のユーザー・グループを確認します。
4. 初めての練習では公開範囲を `Private` にします。
5. 新しく始める場合は `Initialize repository with a README` を選んで作成します。
6. 作成後、既定ブランチの名前と、自分の権限を確認します。

既定ブランチ名は、サーバーやグループの設定によって異なります。常に `main` だと決めつけず、プロジェクトに表示される名前を使います。CI/CDや追加のスキャン機能は、Gitの基本操作とは分けて設定できます。

参考：[プロジェクトの作成](https://docs.gitlab.com/user/project/)

## 2. cloneして作業する

プロジェクトのコード取得メニューから、SSHまたはHTTPSのURLをコピーします。[アカウント編](account.md)のOpenSSH別名を設定済みなら、GitLab.comでは次のようにcloneできます。

```sh
git clone git@gitlab-tutorial:NAMESPACE/PROJECT.git
cd PROJECT
```

`NAMESPACE/PROJECT` は実際のパスへ置き換えます。自前サーバーやHTTPSでは、その接続先の画面からコピーしたURLを使います。`gitlab-tutorial` は前の章で手元に付けた別名で、GitLab全体に共通するホスト名ではありません。

最初のコミットを作る前に、[コミット名義](account.md#4-手元のgitの記録名義を設定する)をこのリポジトリへ設定します。GitHubのnoreplyアドレスではなく、接続先のGitLabで用意したアドレスなどを使います。

作業用のブランチを分ける例です。`topic/add-notes` と `notes.md` は、まだ存在しないものとします。

```sh
git switch -c topic/add-notes
printf '# GitLabでの練習\n' > notes.md
git add notes.md
git diff --cached
git commit -m "Add GitLab practice notes"
git push -u origin topic/add-notes
```

既定ブランチは保護されている場合があります。直接pushが拒否されたときは、forceで上書きしたり保護を外したりせず、許可された作業用ブランチとMerge Requestを使います。

TortoiseGitでも、URLと認証方式をGitLab用に合わせれば [clone・コミット・送受信](../tortoisegit/README.md)の流れは同じです。

## 3. Merge Requestで取り込む

1. 作業用ブランチをpushしたあと、GitLabの案内またはプロジェクトのMerge Request作成画面を開きます。
2. 送り元を `topic/add-notes`、取り込み先を実際の既定ブランチなどにします。
3. 変更の目的、差分、レビューする相手を確認して提案を作成します。
4. レビュー・競合解決・必要なチェックを経て、権限のある人がマージします。
5. 手元でも取り込み先のブランチへ切り替え、fetch・pullで更新を確認します。

グループやプロジェクトの役割によって、pushやマージが許される範囲は異なります。自分だけのプロジェクトと、共有プロジェクトの権限を区別しましょう。

参考：[Merge Requestの作成](https://docs.gitlab.com/user/project/merge_requests/creating_merge_requests/)、[保護ブランチ](https://docs.gitlab.com/user/project/repository/branches/protected/)

## 既存の履歴を送る場合

既存のリポジトリを移すなら、GitLab側でREADMEなどの初期コミットを作らず、空のプロジェクトを用意する方法があります。GitLabの画面が示すURLをリモートに追加し、必要なブランチ・タグを送ります。

これはファイルだけでなく、過去のコミット名義や削除済みの内容も送る操作です。[公開前の履歴確認](../PRIVACY.md)を行い、両側に独立した初期コミットがある場合は、使う履歴を先に整理します。

Gitのpushだけでは、ほかのサービスのIssue・レビュー・権限・CI設定まで同じ形で移りません。移行機能を使う場合は、移る対象と認証情報の扱いを別に確認してください。
