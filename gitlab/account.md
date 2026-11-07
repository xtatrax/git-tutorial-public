# GitLab.comの登録と初期設定

[全体の案内へ戻る](../README.md) · [GitLabの概要とプロジェクト作成](README.md) · [自前サーバー構築](self-managed.md)

この章では、GitLabが運営するGitLab.comへ登録し、公開情報・認証・手元のGitを設定します。自前のGitLabは別のサービスなので、GitLab.comへ登録しただけで自前サーバーにもログインできるわけではありません。

公式資料の確認日：2026年11月7日。画面の位置や項目は変更されることがあるため、表示された案内と公式資料も確認してください。実際のアカウントや認証情報の画像は使っていません。

## 1. アカウントを作る

1. [GitLab.comの登録ページ](https://gitlab.com/users/sign_up)を開きます。
2. 氏名・ユーザー名・受信できるメールアドレスなど、画面に表示される項目を入力します。パスワードを設定する場合は、ほかのサービスと使い回さないものを使います。
3. Google・GitHubなど、画面に表示される外部サービス経由の登録を使う方法もあります。
4. 利用条件や確認事項を読み、登録とメール確認の案内を完了します。
5. 利用目的やプロジェクトの案内が出たら、用途に合わせて進めます。不要な有料契約や追加サービスを選ぶ必要はありません。
6. ログイン後、自分のユーザー名と現在のプランを確認します。

ユーザー名はプロフィールやプロジェクトのURLに使われる公開の識別名です。登録用メールと、プロフィールで他の人へ表示するメールは別の設定として扱います。教材の `example.invalid` は説明用なので、メール確認には使えません。

基本的なGit操作や小規模な練習はFreeで進められます。試用プランへ案内された場合は、選択した内容・期間・終了後の扱いを確認します。機能や利用上限は [現在のプラン案内](https://about.gitlab.com/pricing/)を参照してください。

登録時の本人確認では、状況によりメールに加えて電話番号やカードによる確認が求められることがあります。本人確認と有料プランの契約は別です。「全員にカードが必要」「絶対にカード不要」のどちらとも決めつけず、公式画面の案内を確認します。

参考：[本人確認](https://docs.gitlab.com/security/identity_verification/)

## 2. プロフィールとメールの公開範囲を設定する

右上のアバターから `Edit profile` を開きます。

| 設定 | 確認すること |
| --- | --- |
| 表示名・画像・自己紹介 | 公開してよい内容にする。私用の所在地や内部情報を不用意に入れない |
| 登録メール | 自分が受信・確認できるものを維持する |
| `Public email` | 実メールを公開しない方針なら、公開しない状態にする |
| `Commit email` | Web操作で記録するコミット用メールを選ぶ |

実メールをコミットに出したくない場合は、次のように設定します。

1. `Commit email` の選択肢で `Use a private email` を選びます。
2. `Update profile settings` で保存します。
3. 表示される自分用の非公開コミットメールを確認し、手元のGitへ設定するためにコピーします。形式や番号を推測して作らないでください。

これはGitLabのWeb操作に使う設定です。手元のGitの `user.email` は、後の節で別途設定します。既存のコミットに記録済みの情報も変更されません。

非公開コミットメールは実メールを出さないためのもので、アカウントのユーザー名や活動を匿名にするものではありません。プロフィールを非公開にする機能も、公開プロジェクト・コミット・APIなどの全情報を隠す機能ではありません。

参考：[プロフィールとコミットメール](https://docs.gitlab.com/user/profile/)

## 3. 2段階認証と復旧方法を設定する

この教材では、認証アプリのTOTPを使う例を説明します。

1. アバターから `Edit profile` を開きます。
2. 左側の `Access → Password and authentication` へ進みます。
3. `One-time password authenticator` の `Register authenticator` を選びます。
4. GitLabのQRコードを認証アプリで読み取るか、表示されたセットアップ情報をアプリへ登録します。
5. 求められた認証情報とアプリの確認コードを入力し、登録を完了します。
6. 表示された復旧コードを、安全な場所に保存します。

QRコードやセットアップ用の秘密情報、復旧コードは、リポジトリ・教材・共有画像に保存しません。復旧コードは認証手段が使えない場合のために保管し、各コードは1回だけ使えます。端末を失っても取り出せる保管方法を用意します。

対応する端末・ブラウザーでは、同じ設定画面の `Passkey sign-in` からパスキーも追加できます。利用できる方法や保管・復元の仕組みは、端末と提供元に合わせて確認します。

登録後は現在のログインを残したまま、別のブラウザープロファイルなどで認証できるか確認します。GitLab.comではメールの追加確認が表示される場合もあるため、登録メールを受信できる状態も維持します。

参考：[2段階認証と復旧コード](https://docs.gitlab.com/user/profile/account/two_factor_authentication/)、[パスキー](https://docs.gitlab.com/auth/passkeys/)

## 4. 手元のGitの記録名義を設定する

この操作は、ローカルリポジトリを作成・cloneしたあと、そのディレクトリで最初のコミットを作る前に行います。まだ作業場所がない場合は、先に接続準備と [プロジェクト作成](README.md)へ進み、clone後にこの節へ戻ってください。

`Sample Contributor` は自分が公開する名義、`YOUR_GITLAB_PRIVATE_COMMIT_EMAIL` はプロフィールからコピーした自分用の非公開コミットメールに置き換えてから実行します。

```sh
git config --local user.name "Sample Contributor"
git config --local user.email "YOUR_GITLAB_PRIVATE_COMMIT_EMAIL"
git config --get user.name
git config --get user.email
```

`--local` は現在のリポジトリに適用され、ほかのリポジトリの設定は変更しません。GitHubなどで作業するリポジトリもある場合は、接続先に応じた名義をそれぞれ確認します。

`user.name` はコミットに記録する名義で、ログイン名やパスワードではありません。ほかの章の `contributor@example.invalid` はローカル実習用です。自分のGitLabアカウントへコミットを関連付ける場合は、アカウントに対応するアドレスを設定します。

設定変更は、過去のコミットの作成者・コミッター情報を修正する操作ではありません。

## 5. SSH公開鍵を登録する

SSHを使う場合は、GitLab用の鍵を用意します。次の例は、同名の秘密鍵と公開鍵のどちらもまだ存在しない場合だけ、新しい鍵を作ります。

```sh
mkdir -p ~/.ssh
if [ ! -e ~/.ssh/gitlab_tutorial_ed25519 ] && [ ! -e ~/.ssh/gitlab_tutorial_ed25519.pub ]; then
    ssh-keygen -t ed25519 -f ~/.ssh/gitlab_tutorial_ed25519 -C "gitlab-tutorial-example"
else
    printf '同名の鍵があります。用途を確認してから進めてください。\n'
fi
```

作成時はパスフレーズを設定します。すでに鍵がある場合は、用途を確認し、上書きや作り直しをせずに使う鍵を決めます。

```sh
cat ~/.ssh/gitlab_tutorial_ed25519.pub
```

表示された公開鍵をコピーし、GitLab側で登録します。

1. アバターから `Edit profile` を開きます。
2. 左側の `Access → SSH keys` から、新しい鍵の追加を選びます。
3. 公開鍵を貼り付け、`Tutorial key` など用途の分かるタイトルを入力します。
4. Gitの送受信だけに使うなら、用途に `Authentication` を選びます。有効期限も必要に応じて確認します。
5. 内容を確認し、鍵を追加します。

登録するのは `.pub` 側だけです。秘密鍵・2FAのセットアップ情報・復旧コードを貼り付けないでください。

参考：[GitLabでのSSH鍵利用](https://docs.gitlab.com/user/ssh/)

### 鍵を選ぶ接続設定

ホームディレクトリの `.ssh/config` をエディターで開き、既存の内容を残して次の未使用の別名を追加します。ファイル名が `config.txt` にならないようにします。

```sshconfig
Host gitlab-tutorial
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/gitlab_tutorial_ed25519
    IdentitiesOnly yes
```

`gitlab-tutorial` は手元の接続設定に付けた別名です。同名の設定がすでにある場合は確認して、別の未使用名を選びます。GitLab側のユーザー名にかかわらず、この接続先ユーザーは `git` です。

```sh
ssh -T gitlab-tutorial
```

初回接続では、ホスト鍵の種類とフィンガープリントを [GitLab.comの公式情報](https://docs.gitlab.com/user/gitlab_com/#ssh-host-keys-fingerprints)と照合してから許可します。成功すると、自分のユーザー名を含むWelcomeメッセージが表示されます。GitHubの接続テストとは分けて、GitLabの案内とメッセージを確認してください。

clone時は `git@gitlab-tutorial:NAMESPACE/PROJECT.git` の形式で、この別名を使います。`NAMESPACE` と `PROJECT` は実際のプロジェクト画面の値に合わせます。グループ配下ではnamespaceが複数階層になる場合もあります。

WindowsのTortoiseGitでは、`Settings → Network` のSSHクライアントをOpenSSHの `ssh.exe` にそろえると、この設定を使えます。PuTTY系の設定とは分けて、[TortoiseGitのSSH設定](../tortoisegit/README.md#sshを使う場合)も確認してください。

## 6. HTTPSを使う場合

HTTPSでは、Git Credential ManagerなどのOAuth対応ツールで認証する方法もあります。GitLab.comへのGCMのOAuth認証は、公式に案内されています。

Personal access token（PAT）を使う場合は、有効期限と必要最小限の権限を設定します。従来型トークンの設定例では、`Edit profile → Access → Personal access tokens` のトークン生成メニューから `Legacy token` を選びます。

| 権限 | Gitでできること |
| --- | --- |
| `read_repository` | 非公開リポジトリなど、権限のある対象から取得する |
| `write_repository` | 権限のあるリポジトリで取得とpushを行う |

Gitの送受信だけに広い `api` 権限は不要です。PATで認証する場合は、ユーザー名欄に空でない値、パスワード欄にトークンを使います。トークンはリモートURLへ埋め込まず、認証ツールなどで管理します。

2FAやSAMLを使う場合、通常のパスワードでGitのHTTPS認証を行う代わりに、PATや対応するOAuth認証を使います。ブラウザーへのログインだけで、手元のGitの認証まで設定されるわけではありません。

参考：[OAuth認証ツール](https://docs.gitlab.com/user/profile/account/two_factor_authentication/#oauth-credential-helpers)、[PATの作成](https://docs.gitlab.com/user/profile/personal_access_tokens/)、[権限の種類](https://docs.gitlab.com/security/tokens/access_token_scopes/)

## 次へ進む前の確認

- ログインとメール確認を完了し、公開するプロフィールとメール設定を確認した。
- 認証アプリなどを登録し、復旧方法を保管した。
- SSHまたはHTTPSの接続準備をした。
- ローカルリポジトリがある場合は名義を確認した。これからcloneする場合は、最初のコミット前に設定する。

ここまでできたら、[GitLabのプロジェクト作成と送受信](README.md)へ進みます。公開前には、[ファイルと過去の履歴に残る情報](../PRIVACY.md)も確認してください。
