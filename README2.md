# 準備とSSH

[全体の案内へ戻る](README.md)

## Gitのインストール

[Git公式のインストール案内](https://git-scm.com/install)から、使っているOSに合う方法を選びます。WindowsではGit for WindowsのGit Bashを使うと、この教材のシェル例を実行できます。macOS・Linuxではターミナルを使います。

インストール後、次のコマンドで確認します。

```sh
git --version
git --help
```

この教材では `git switch` と `git restore` が使えるGit 2.23以降を前提とします。

## コミットに記録する名義

Gitはコミットに名前とメールアドレスを記録します。GitHubへのログイン情報とは別です。

次の設定は、リポジトリを作成またはクローンしたあと、そのディレクトリで実行します。

```sh
git config --local user.name "Sample Contributor"
git config --local user.email "contributor@example.invalid"
```

これは実習用の架空の名義です。実際の共同作業では、公開してよい名前とアドレスに置き換えます。実メールを公開したくない場合は、GitHubの設定に表示される自分用のnoreplyアドレスも使えます。アドレスの形式は推測せず、設定画面で確認してください。

GitHubのアカウント作成やメールの非公開設定は、[GitHubの初期設定](github/account.md)を参照してください。

設定の適用範囲は次のとおりです。

| オプション | 適用範囲 |
| --- | --- |
| `--local` | 現在のリポジトリ |
| `--global` | OSのログインユーザー |
| `--system` | システム全体 |

この教材では、他のリポジトリの設定を変えない `--local` を使います。

```sh
git config --get user.name
git config --get user.email
```

名義の設定を変えても、過去のコミットに記録された名前・メールアドレスは変わりません。

参考：[GitHubのコミット用メールアドレスの設定](https://docs.github.com/en/account-and-profile/how-tos/email-preferences/setting-your-commit-email-address)

## SSH鍵を作る

SSHはリモートへの接続方法の一つです。[ローカル実習](git/README.md)では必要ありません。

以下は、`~/.ssh/git_tutorial_ed25519` がまだ存在しない場合の例です。既存の鍵を上書きしないよう、先にファイルの有無を確認します。

```sh
mkdir -p ~/.ssh
ssh-keygen -t ed25519 -f ~/.ssh/git_tutorial_ed25519 -C "git-tutorial-example"
```

パスフレーズを設定し、安全に保管します。

| ファイル | 扱い |
| --- | --- |
| `git_tutorial_ed25519.pub` | 公開鍵。接続先のサービスや管理者に登録する |
| `git_tutorial_ed25519` | 秘密鍵。共有・コミットしない |

登録するのは `.pub` 側です。秘密鍵をREADMEやチャットに貼り付けないでください。

GitHubへの登録操作と接続テストは、[GitHubへのSSH接続準備](github/account.md#6-手元のgitからの接続を用意する)で説明しています。

## クローン時に鍵を指定する

次の `OWNER` と `REPOSITORY` は、接続先のアカウント・組織名とリポジトリ名に置き換えます。

```sh
git -c core.sshCommand="ssh -i ~/.ssh/git_tutorial_ed25519 -o IdentitiesOnly=yes" clone git@github.com:OWNER/REPOSITORY.git
```

`-c` の設定は、このコマンドだけに適用されます。以後も同じ鍵を指定する場合は、クローン先で設定します。

```sh
cd REPOSITORY
git config --local core.sshCommand "ssh -i ~/.ssh/git_tutorial_ed25519 -o IdentitiesOnly=yes"
```

初回接続時には、接続先の公式資料でホスト鍵のフィンガープリントを照合します。

参考：[GitHubへのSSH接続](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
