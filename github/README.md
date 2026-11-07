# GitHubを使う

[全体の案内へ戻る](../README.md) · [アカウント作成と初期設定](account.md) · [コマンドの実習](../git/README.md)

GitHubはGitリポジトリのホスティングサービスです。履歴の共有に加え、Issueでの課題管理、Pull Requestでの変更レビューなどに使えます。

初めて使う場合は、[アカウント作成と初期設定](account.md)を先に進めてください。メールの確認・公開範囲・2FA・手元のGitからの接続を準備できます。

## リポジトリを作る

1. 自分のアカウントや組織で、新しいリポジトリを作成します。
2. 名前と公開範囲を選びます。公開リポジトリのファイルや履歴は、他の人が閲覧・複製できます。
3. 空のリポジトリから始めるか、README・ライセンス・`.gitignore` を用意して始めるか選びます。
4. 作成後に表示されるURLと案内を使って、ローカルと接続します。

アカウント名・メールアドレス・認証トークンが写り込むため、この教材では実際のアカウント画面の画像を使っていません。

### GitHubでREADMEなどを作った場合

すでにコミットがあるので、まず `git clone` で取得し、クローンしたディレクトリで編集します。

```sh
git clone https://github.com/OWNER/REPOSITORY.git
cd REPOSITORY
```

`OWNER` と `REPOSITORY` は、接続先のアカウント・組織名とリポジトリ名に置き換えます。

clone後、最初のコミットを作る前に、[手元のGitの記録名義](account.md#5-手元のgitの記録名義を設定する)を設定します。GitHubへ関連付ける場合は、自分用のnoreplyアドレスなど、アカウントに対応するアドレスを使ってください。

### 手元のリポジトリを空のGitHubリポジトリへ送る場合

手元でコミット済みで、ブランチ名が `main`、まだ `origin` がない場合の例です。

```sh
git remote add origin https://github.com/OWNER/REPOSITORY.git
git push -u origin main
```

両側に別々の初期コミットがある場合は、そのままでは同じ手順になりません。初めての練習なら、GitHubで初期ファイルを作ってクローンする方法にそろえると進めやすくなります。

HTTPSではサービスが案内する認証方法を使います。トークンをリモートURLや教材のファイルへ埋め込まないでください。SSHを使う場合は [準備とSSH](../README2.md)を参照してください。

## 変更を提案する流れ

1. 共有先の最新状態を取得する。
2. 作業用ブランチを作る。
3. 編集内容を確認してコミットする。
4. 作業用ブランチをpushする。
5. Pull Requestを作り、目的と変更内容を説明する。
6. レビューと必要な修正を経て、変更を統合する。

書き込み権限がないリポジトリでは、forkを作って自分のforkへpushし、元のリポジトリへPull Requestを送る方法があります。参加先の案内やルールを確認しましょう。

## GitHub Pages

GitHub Pagesは、リポジトリの内容から静的なWebサイトを公開する仕組みです。HTML・CSS・JavaScriptなどを配信できます。PHPなどのサーバー側プログラムをそのまま実行するためのサービスではありません。

- ユーザー・組織サイトのリポジトリ名は `OWNER.github.io`。
- プロジェクトごとのリポジトリから公開する方法もある。
- リポジトリを作っただけで完了するとは限らず、Pagesの公開元などを設定する。

利用できる条件や設定手順は、公式資料で確認してください。

## 公式資料

- [リポジトリの作成](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository)
- [認証について](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)
- [Pull Requestについて](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
- [GitHub Pagesについて](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages)
