# GitHubのアカウント作成と初期設定

[全体の案内へ戻る](../README.md) · [GitHubでリポジトリを作る](README.md#リポジトリを作る) · [準備とSSH](../README2.md)

この章では、GitHub.comの個人アカウントを作り、公開情報・認証・手元のGitを設定します。会社や学校から管理されたアカウントを提供されている場合は、その案内に従ってください。

画面や入力順序は変わることがあるため、項目名と公式資料を目印に進めます。この教材では、実際のアカウント画面や認証情報の画像を使いません。

公式資料の確認日：2026年11月7日。

## 最初に区別しておくもの

| 項目 | 役割・公開範囲 |
| --- | --- |
| 登録するメールアドレス | GitHubからの確認や連絡に使う。公開用の表示設定とは別 |
| ユーザー名 | プロフィールやリポジトリのURLに使う公開の識別名 |
| プロフィールの表示名 | 他の人に見せる名前。実名と一致させる必要はない |
| Gitの `user.name` と `user.email` | コミットに記録する名義。ログイン情報とは別 |
| パスワード・2FA・パスキー | ブラウザーなどでアカウントへログインするための情報・方法 |
| SSH鍵 | 手元のGitからGitHubへ接続するための認証方法の一つ |

登録用メールには、自分が受信して確認できるアドレスを使います。教材中の `example.invalid` は説明用なので、アカウント作成には使えません。

## 1. アカウントを作る

1. [GitHubの登録ページ](https://github.com/signup)を開きます。
2. メールアドレスなどの入力、または画面に表示されるGoogle・Appleなどの対応する登録方法を選びます。
3. 公開してよいユーザー名を選び、画面の案内に沿って必要な情報を入力します。パスワードを設定する場合は、他のサービスと使い回さないものにします。
4. 本人確認や利用条件の確認など、表示された手順を完了します。
5. メールの確認を求められたら、受信した案内に従って確認を完了します。
6. ログイン後、自分のユーザー名とプロフィールを確認します。

この教材の基本的な練習はGitHub Freeで進められます。有料プランや追加サービスの契約は、必要な機能が決まってから検討できます。

すでに自分のアカウントがある場合は、それにログインして次の設定へ進みます。

参考：[アカウント作成の公式手順](https://docs.github.com/en/account-and-profile/how-tos/account-management/creating-an-account-on-github)、[GitHubのプラン](https://docs.github.com/en/get-started/learning-about-github/githubs-plans)

## 2. メール確認と非公開設定

右上のプロフィール画像から `Settings` を開き、`Emails` へ進みます。

まず登録したアドレスが確認済みか見ます。未確認の場合は `Resend verification email` で再送し、届いた案内から確認します。

実メールを公開したくない場合は、同じ画面で次の2項目を有効にします。

| 項目 | 何をするか |
| --- | --- |
| `Keep my email addresses private` | GitHubのWeb操作で使うコミット用メールをnoreplyにし、プロフィール上の実メール公開を防ぐ |
| `Block command line pushes that expose my email` | 自分の非公開メールが含まれるコマンドラインからのpushを、一部の条件で拒否する |

この画面に表示される、自分用のGitHub提供noreplyアドレスも確認しておきます。あとで手元のGitに設定するため、表示されたものをそのままコピーします。形式や番号を推測して作らないでください。

pushのブロック機能が確認するのは、最新コミットの作成者メールが、自分のアカウントの非公開メールかどうかです。過去の全コミット、コミッター情報、ファイル本文に含まれるメールや秘密情報をまとめて検査する機能ではありません。

また、メールの非公開設定は手元のGit設定を自動では変更しません。すでにあるコミットに記録された情報も消えません。

参考：[メールアドレスの確認](https://docs.github.com/en/account-and-profile/how-tos/email-preferences/verifying-your-email-address)、[コミット用メールの設定](https://docs.github.com/en/account-and-profile/how-tos/email-preferences/setting-your-commit-email-address)、[メールを含むpushの保護](https://docs.github.com/en/account-and-profile/how-tos/email-preferences/blocking-command-line-pushes-that-expose-your-personal-email-address)

## 3. 公開プロフィールを整える

設定の `Public profile` を開き、公開する内容を確認して保存します。

- 表示名は、公開してよいニックネームなどにする。
- 公開メール欄は、実メールを公開しない方針なら表示しない状態にする。
- 自己紹介・所在地・会社・外部サイトなどは、共有したい内容だけ記入する。
- プロフィール画像も、公開してよいものを使う。自分の写真を登録する必要はない。

表示名とユーザー名は別の項目です。表示名を変更しても、URLのユーザー名が同じように変わるわけではありません。

プロフィールを非公開にする機能もありますが、自己紹介・画像・プロフィールREADMEなど、引き続き公開される項目があります。公開リポジトリやそこに残るコミットまで、まとめて非公開になる設定ではありません。

参考：[プロフィールの設定](https://docs.github.com/en/account-and-profile/tutorials/personalize-your-profile)、[プロフィールの公開範囲](https://docs.github.com/en/account-and-profile/concepts/personal-profile)

## 4. 2段階認証と復旧方法を設定する

アカウントのセキュリティ設定で、2段階認証（2FA）を用意します。この教材では認証アプリのTOTPを使う流れを説明します。すでに設定済みなら、利用できる認証方法と復旧方法を確認します。

1. 設定の `Password and authentication` を開きます。
2. 2FAの有効化を選び、認証アプリを使う設定へ進みます。
3. GitHubに表示されたQRコードを認証アプリで読み取るか、セットアップ用のキーを登録します。
4. アプリに表示される確認コードをGitHubへ入力します。
5. 復旧コードを安全な場所に保存し、画面の案内に沿って設定を完了します。

QRコードとセットアップ用のキーは、認証コードを生成するための秘密情報です。復旧コードとともに、教材・リポジトリ・共有画像へ載せないでください。

復旧コードは認証手段を使えなくなったときのために保管します。各コードは1回だけ使えます。新しい一式を発行すると以前の一式は無効になります。端末を失ったときにも取り出せるよう、信頼できるパスワード管理ツールなどで管理します。

対応する端末・ブラウザーでは、同じ設定画面のパスキー追加からパスキーも登録できます。保存先や端末紛失時の復元方法は提供元によって異なるため、それも確認します。複数の認証方法を用意すると、1つの端末を使えなくなったときに備えられます。

設定後は、現在のログインを残したまま別のブラウザープロファイルなどでサインインを試し、用意した認証方法が使えるか確かめると安心です。確認のために復旧コードを消費する必要はありません。

参考：[2FAの設定](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication)、[復旧方法](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication-recovery-methods)、[パスキーの管理](https://docs.github.com/en/authentication/authenticating-with-a-passkey/managing-your-passkeys)

## 5. 手元のGitの記録名義を設定する

Gitのインストールは [準備とSSH](../README2.md)を参照してください。設定は、リポジトリを作成またはcloneしたあと、そのディレクトリで最初のコミットを作る前に行います。

まだ手元にリポジトリがない場合は、この節の操作はあとで行います。先に接続の準備と [リポジトリ作成](README.md#リポジトリを作る)へ進み、作成またはcloneしたディレクトリでこの設定をしてください。

次の `Sample Contributor` は自分が公開する名義、`YOUR_GITHUB_NOREPLY_EMAIL` はGitHubのメール設定からコピーした自分用noreplyアドレスへ置き換えてから実行します。

```sh
git config --local user.name "Sample Contributor"
git config --local user.email "YOUR_GITHUB_NOREPLY_EMAIL"
git config --get user.name
git config --get user.email
```

`--local` は現在のリポジトリだけに適用されます。別のリポジトリでも、最初のコミット前に設定を確認します。

`user.name` はコミットの表示名で、GitHubのログイン名とは別です。アカウントとの関連付けにはコミットのメールアドレスが使われます。ほかの章の `contributor@example.invalid` はローカル実習用の架空の名義なので、自分のGitHubアカウントへ関連付けるときは置き換えます。

noreplyは実メールの公開を避けるためのもので、GitHubのユーザー名や活動を匿名にするものではありません。設定変更は、過去のコミットの作成者・コミッター情報には反映されません。

参考：[Gitのコミット用メールの設定](https://docs.github.com/en/account-and-profile/how-tos/email-preferences/setting-your-commit-email-address)

## 6. 手元のGitからの接続を用意する

ブラウザーへログインしただけで、手元のGitからのpushが認証済みになるわけではありません。使うリモートURLに合う方法を用意します。

| 方法 | 接続準備 |
| --- | --- |
| SSH | 手元で鍵を用意し、公開鍵をGitHubへ登録する |
| HTTPS | ブラウザー認証に対応するツールや、認証情報を管理するツールなどを使う |

GitのHTTPS認証には、GitHubのログイン用パスワードは使えません。トークンを使う場合も、URL・教材・コミットへ埋め込まないでください。

### SSH公開鍵を登録する

[準備とSSH](../README2.md#ssh鍵を作る)で作った `git_tutorial_ed25519` を使う例です。すでに鍵がある場合は、作り直したり上書きしたりせず、その鍵の用途を確認します。

```sh
cat ~/.ssh/git_tutorial_ed25519.pub
```

表示された公開鍵をコピーして、GitHub側で次のように登録します。

1. 設定の `SSH and GPG keys` を開きます。
2. 新しいSSH鍵の追加を選びます。
3. タイトルは `Tutorial key` など、用途が分かる名前にします。
4. 鍵の種類は認証用を選び、コピーした公開鍵を貼り付けます。
5. 追加を確定し、必要ならアカウントの認証を行います。

登録するのは `.pub` 側だけです。秘密鍵や2FAのセットアップ用キーを貼り付ける場所ではありません。

### SSH接続を確認する

```sh
ssh -T -i ~/.ssh/git_tutorial_ed25519 -o IdentitiesOnly=yes git@github.com
```

初回接続時は、表示されたホスト鍵の種類とフィンガープリントを [GitHub公式の一覧](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)と照合してから、接続を許可します。照合せずにホスト鍵確認を無効化しないでください。

成功すると、挨拶のメッセージに自分のユーザー名が表示されます。GitHubは通常のシェルを提供しないため、このテストは認証成功でも終了コードが `1` になります。終了コードだけでなく、メッセージとユーザー名を確認します。

接続先の `git@github.com` の `git` は、自分のユーザー名に置き換えません。実際のcloneでも同じ鍵を指定する方法は [準備とSSH](../README2.md#クローン時に鍵を指定する)を参照してください。

参考：[SSH公開鍵の登録](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)、[SSH接続テスト](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)、[接続方法ごとの認証](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)

## 初期設定の確認

- 自分のアカウントへログインでき、登録メールの確認が済んでいる。
- 公開する名前・プロフィール・メール設定を確認した。
- 2FAなどの認証方法が使え、復旧コードを保管した。
- 手元にリポジトリがある場合は、コミット名義を確認した。これから作成・cloneする場合は、最初のコミット前に確認する。
- 手元のGitを使う場合は、SSHなどの接続方法を用意した。

ここまでできたら、[GitHubでリポジトリを作る](README.md#リポジトリを作る)へ進みます。初めての練習で公開範囲を迷う場合はPrivateから始められます。Publicへ変更する前には、[履歴も含めた公開用データの確認](../PRIVACY.md)を行いましょう。
