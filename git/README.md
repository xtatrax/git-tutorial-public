# コマンドで使うGit

[全体の案内へ戻る](../README.md) · [準備とSSH](../README2.md)

最初はリモートへ接続せず、手元だけで変更を記録してみましょう。シェル例はGit Bash、macOS、Linuxのターミナル向けです。

## 1. 練習用リポジトリを作る

`git-practice` というディレクトリがまだ存在しない場所で実行します。

```sh
mkdir git-practice
cd git-practice
git init -b main
git config --local user.name "Sample Contributor"
git config --local user.email "contributor@example.invalid"
git status
```

`git init -b main` の `-b` はGit 2.28以降で使えます。それより前なら `git init` のあと、最初のコミットを作ってから `git branch -M main` で名前をそろえます。

## 2. ファイルを作り、ステージする

```sh
printf '# 練習ノート\n' > notes.md
git status --short
git add notes.md
git diff --cached
```

`git add` は、その時点のファイル内容をステージに登録します。ステージ後にさらに編集した場合、その追加の編集はもう一度 `git add` するまで次のコミットに入りません。

| コマンド | 確認する内容 |
| --- | --- |
| `git status --short` | ファイルの状態。新規未追跡は `??` |
| `git diff` | 作業ツリーとステージとの差分。未追跡ファイルの内容は表示しない |
| `git diff --cached` | ステージと直前のコミットとの差分。初回は追加内容 |

`git add .` は現在のディレクトリ以下を対象にします。リポジトリ全体を対象にするなら `git add -A` ですが、最初はファイルを明示して、意図しない追加を防ぎましょう。

参考：[git addの公式説明](https://git-scm.com/docs/git-add)

## 3. コミットする

```sh
git commit -m "Add practice notes"
git status
git log --oneline
```

`git status` に作業ツリーがクリーンであると表示されれば、未記録の変更はありません。このコミットは手元だけに保存されています。

## 4. 変更して、差分を確かめる

```sh
printf '\nGitで変更を記録する。\n' >> notes.md
git diff
git add notes.md
git diff --cached
git commit -m "Explain version history"
git log --oneline --graph --all
```

`git show HEAD` で直近のコミット、`git diff HEAD~1 HEAD` で直近2つのコミットの差分も確認できます。

## 5. ブランチを作って統合する

```sh
git switch -c topic/add-example
printf '\nブランチで作業を分ける。\n' >> notes.md
git add notes.md
git commit -m "Add branch example"
git switch main
git merge topic/add-example
git log --oneline --graph --all
```

ブランチは作業の流れを分ける目印です。この例では `main` 側に別の変更がないため、通常はfast-forwardで先へ進みます。

別々のブランチで同じ箇所を変更すると、マージ時に競合することがあります。その場合は `git status` で対象を確認し、内容を整理してから `git add` と `git commit` を行います。マージ中の作業を取りやめるには `git merge --abort` を使います。

## 6. 取り消しは対象を確かめてから

ステージしただけの変更をステージから外すには、次を使います。ファイルの編集内容は残ります。この例は、すでにコミットがあるリポジトリ向けです。

```sh
git restore --staged notes.md
```

追跡中のファイルの未ステージの編集を捨て、ステージと同じ内容へ戻すには次を使います。必要な編集は先に別の場所へ保存してください。

```sh
git diff -- notes.md
git restore -- notes.md
```

共有済みのコミットの変更を打ち消したい場合は、履歴を削る代わりに、打ち消しのコミットを作る `git revert` が使えます。競合する場合もあるため、対象のコミットを確認してから実行します。

```sh
git show HEAD
git revert HEAD
```

この例は直近の通常のコミットを対象とします。マージコミットの取り消しには追加の判断が必要です。

## 7. 既存のリポジトリをクローンする

既存のリポジトリに参加する場合は `git init` ではなく `git clone` を使います。次のURLは説明用なので、接続先の実際のURLに置き換えます。

```sh
git clone https://example.invalid/OWNER/REPOSITORY.git
cd REPOSITORY
git config --local user.name "Sample Contributor"
git config --local user.email "contributor@example.invalid"
git remote -v
```

通常、クローン元は `origin` というリモート名で登録されます。`git remote -v` の出力には接続先の情報が含まれるので、共有する際は内容を確認します。

## 8. リモートと同期する

ここからは、接続できるリモートと、リモート側の `main` ブランチがある場合の例です。まず作業中の変更をコミットするなどして、作業ツリーを整理します。

```sh
git switch main
git fetch origin
git log --oneline --graph --all
git pull --ff-only origin main
```

`fetch` は履歴の取得、`pull` は取得と現在のブランチへの統合です。`--ff-only` はfast-forwardできる場合だけ統合し、履歴が分岐していたら停止します。停止したら双方の変更を確認して、マージなどの方法を選びます。

ローカルのコミットを送る場合は次を使います。

```sh
git push origin main
```

リモート側が先に進んでいて拒否された場合は、`fetch` で状況を確認します。力ずくの上書きは、他の人の履歴を失わせる可能性があります。

ローカルで作成し、コミット済みの作業用ブランチを初めて送る場合は、次のように追跡先を設定できます。次の例では、ローカルに `topic/add-example` があることを前提とします。

```sh
git push -u origin topic/add-example
```

送る前に、[個人情報と履歴の確認](../PRIVACY.md)も行いましょう。

参考：[git pullの公式説明](https://git-scm.com/docs/git-pull)

## ほかに知っておくと便利な機能

| コマンド・機能 | 用途 |
| --- | --- |
| `git mv old-name.md new-name.md` | 追跡中のファイルを移動し、その変更をステージする |
| `git tag v1.0.0` | 現在のコミットに名前を付ける。タグの共有には別途pushが必要 |
| `git stash push -m "work in progress"` | 未コミットの変更を一時退避する。未追跡ファイルも含める場合は `-u` |
| `git stash list` / `git stash pop` | 退避を一覧表示する／適用し、成功すれば退避を削除する |
| fork | ホスティングサービス側で別の所有者のリポジトリを派生させる仕組み |

詳しい使い方は `git help コマンド名`、または [Gitの公式ドキュメント](https://git-scm.com/docs)で確認できます。

## 次に試す

手元の履歴をブラウザーで見る場合は、[Git付属のWeb UI：Gitweb](../gitweb/README.md)を使えます。

GitHubを使わずに共有先を作る場合は、[ローカルのベアリポジトリ](../bare/README.md)で練習し、[自前サーバーでのSSH共有](../server/README.md)へ進めます。
