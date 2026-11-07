# GitLabの自前サーバーをセットアップする

[全体の案内へ戻る](../README.md) · [GitLabの使い方](README.md) · [利用者の初期設定](account.md)

この章は、新しいUbuntu 24.04 LTSのサーバー・VMへ、Linuxパッケージ版のGitLab Community Edition（`gitlab-ce`）を導入する手順です。サーバー管理者が更新・バックアップ・利用者管理も担当します。GitLab.comのアカウントを作るだけなら [公式ホスティングの登録手順](account.md)へ進んでください。

公式資料の確認日：2026年11月7日。対応OS・要件・設定方法は、導入するバージョンの公式資料でも確認してください。

## 1. 専用のホストと資源を用意する

[SSHだけのベアサーバー](../server/README.md)とは、別の新規ホストを使う前提です。GitLabのLinuxパッケージはOSの `git` ユーザーなどを管理します。前章で作った `git-shell` アカウントや、その `authorized_keys` を流用しません。

| 項目 | この教材の前提 |
| --- | --- |
| OS | 新規のUbuntu 24.04 LTS、systemdを使用 |
| 管理権限 | 通常の管理者アカウントから `sudo` を使用 |
| CPU・メモリ | 現行の単一ノード基準は8 vCPU・16 GB RAM |
| ディスク | アプリケーション40 GB、DB 5～12 GBに、リポジトリ・ログ・バックアップなどの容量も加える |
| ホスト名 | 利用者の端末から解決できる、固定のGitLab用ホスト名 |
| HTTPS | 公開DNS＋Let's Encrypt、または自前の証明書と信頼設定 |

メモリを制限した構成には少なくとも8 GBの案内がありますが、調整条件が別にあります。この基本手順は、その調整を省略した低メモリ構成を保証するものではありません。

既存のWebサーバーが80／443番を使っていないか、既存のGitLabや `git` アカウントがないかも確認します。既存環境の移行・更新に、この新規インストール手順を重ねて使わないでください。

参考：[ハードウェア要件](https://docs.gitlab.com/install/requirements/)、[Ubuntuへの導入](https://docs.gitlab.com/install/package/ubuntu/)、[GitLabが使うOSユーザー](https://docs.gitlab.com/omnibus/settings/configuration/#change-the-name-of-the-git-user-or-group)

## 2. ホスト名・ネットワーク・HTTPS方式を決める

以下の `gitlab.example.invalid` は教材用です。実際には、DNSや管理下のhosts設定で解決でき、証明書の名前とも一致するホスト名へ置き換えます。公開DNSや公開CAでは `.invalid` のまま使えません。

同じ管理用ターミナルで使う変数の例です。必ず実際の名前へ置き換えてから進めます。

```sh
GITLAB_FQDN='gitlab.example.invalid'
```

| 方式 | 事前に用意するもの |
| --- | --- |
| A：公開DNS＋Let's Encrypt | 実際のドメイン、正しいDNS設定、証明書検証元から到達できる80／443番 |
| B：LAN・VPN内＋自前証明書 | 内部で解決できるホスト名、その名前に有効な証明書・秘密鍵、CAを信頼する設定 |

利用者向けHTTPSは443番、SSHでのclone・pushは通常22番です。管理用SSHの到達性も維持します。ファイアウォールやルーターのルールは、公開範囲に合わせて設定します。ここでは一律にファイアウォールを有効化したり、すべての送信元へ開放したりするコマンドは使いません。

方式Bでは、LAN内だけの名前でLet's Encryptが自動発行できるとは考えず、次の事前配置を行います。方式Aなら、この事前配置は飛ばして次の節へ進めます。

### 方式B：インストール前に証明書と設定を配置する

ホスト名をSANに含む証明書とチェーン、対応する秘密鍵を用意します。次の `/path/to/...` は、そのファイルの実際の場所へ置き換えます。

```sh
sudo install -d -m 755 /etc/gitlab
sudo install -d -m 700 /etc/gitlab/ssl
sudo install -m 644 /path/to/fullchain.pem "/etc/gitlab/ssl/${GITLAB_FQDN}.crt"
sudo install -m 600 /path/to/private.key "/etc/gitlab/ssl/${GITLAB_FQDN}.key"
sudoedit /etc/gitlab/gitlab.rb
```

新規ホストの設定ファイルへ、次を記載します。ここも実際のホスト名にそろえます。

```ruby
external_url 'https://gitlab.example.invalid'
letsencrypt['enable'] = false
```

```sh
sudo chown root:root /etc/gitlab/gitlab.rb
sudo chmod 600 /etc/gitlab/gitlab.rb
```

証明書名を上の既定の形にすれば、個別の証明書パス設定を追加せず利用できます。ブラウザー・Git・GitLab自身など、接続する側が実際に使う証明書ストアへ発行元CAを信頼させます。WindowsのブラウザーとGitが、常に同じ証明書ストアを使うとは限りません。

これは、初回の自動構成より先に設定と証明書を用意する構成例です。事前配置の扱いは現行の [パッケージ処理](https://gitlab.com/gitlab-org/omnibus-gitlab/-/blob/master/config/templates/package-scripts/postinst.erb)に基づきます。TLS設定の詳細とGitLab側の信頼CA配置は、[手動HTTPSの公式手順](https://docs.gitlab.com/omnibus/settings/ssl/#configure-https-manually)を確認してください。単に `EXTERNAL_URL` を省けば未構成のままになる、という前提では進めません。

## 3. 必要なパッケージとGitLabの配布元を追加する

ここからは方式A・B共通です。SSHの既存設定を維持しながら進めます。

```sh
sudo apt update
sudo apt install curl ca-certificates openssh-server tzdata perl
sudo systemctl enable --now ssh
```

公式の配布元設定スクリプトを保存し、中身を確認してから実行します。

```sh
curl --fail --location \
  https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh \
  --output /tmp/gitlab-ce-repository.sh
less /tmp/gitlab-ce-repository.sh
sudo bash /tmp/gitlab-ce-repository.sh
```

GitLab CEとEEは別のパッケージです。この例の途中で、配布元やパッケージ名だけを混在させません。

## 4. GitLab CEをインストールする

設定したホスト名とHTTPSの前提を確認して実行します。方式Bでは、先に置いた設定と同じURLを指定します。

```sh
sudo EXTERNAL_URL="https://${GITLAB_FQDN}" apt install gitlab-ce
```

初回のインストールでは構成と起動も行われます。方式Aは、既存の証明書を設定していなければLet's Encryptでの発行が試みられます。方式Bは、事前配置した証明書とLet's Encrypt無効設定を使います。

`/etc/gitlab/gitlab.rb` を後から変更したときは、次で反映します。

```sh
sudo gitlab-ctl reconfigure
```

TLSの詳細設定を追加する場合は、使用中のバージョンの書式を確認します。現行の公式資料ではGitLab 19.2以降、NGINX関連設定の階層が変更されているため、古い記事の設定をそのまま混ぜないでください。

参考：[パッケージの導入](https://docs.gitlab.com/install/package/ubuntu/)、[HTTPS設定](https://docs.gitlab.com/omnibus/settings/ssl/)

## 5. 起動とHTTPSを確認する

```sh
sudo gitlab-ctl status
sudo gitlab-rake gitlab:env:info
sudo gitlab-rake gitlab:check SANITIZE=true
curl --fail --head "https://${GITLAB_FQDN}/users/sign_in"
```

サービス状態と診断結果を確認し、ブラウザーでも同じHTTPSのURLを開きます。証明書の名前・有効期限・信頼状態を確認してからログインします。証明書エラーを無視する設定で通過させる手順にはしません。

`SANITIZE=true` は診断でプロジェクト名を表示しないための指定です。出力全体からホスト名やすべての個人情報が消えるわけではないため、そのまま公開しないでください。

参考：[保守・診断コマンド](https://docs.gitlab.com/administration/raketasks/maintenance/)

## 6. 初回管理者でログインし、初期設定する

管理者がサーバーの手元の画面で、初期パスワードを確認します。表示内容をチャット・教材・共有ログへ貼り付けません。

```sh
sudo less /etc/gitlab/initial_root_password
```

Webのユーザー名は `root`、パスワードはこのファイルに記載された値です。このWeb管理者と、OSの管理者・OSの `git` ユーザーは別のものです。

初回ログイン後、rootのパスワードを変更します。生成されたメールアドレスも実際に管理するアドレスへ変更し、後述のSMTPが動いたら受信・確認も済ませます。初期パスワードのファイルは約24時間後に削除されるため、恒久的なパスワード保管先にはしません。確認できなくなった場合は、公式のパスワード再設定手順を使います。

### 利用者登録の方針

1. 右上の `Admin` から管理画面を開きます。
2. `Settings → General → New user account restrictions` を確認します。
3. 管理者が利用者を作る運用なら、`Allow new user accounts` を外して保存します。
4. 自己登録を許可する場合は、管理者承認・メール確認なども含めて設定します。

新規インスタンスでは、登録を許可していても管理者承認が有効な構成があります。「登録許可」と「承認不要」は別です。

日常の作業用には別の一般ユーザーを使う方針にします。実際の作成は、次のSMTP設定と送信確認が済んでから行います。rootは管理用にし、通常のclone・pushには使いません。

参考：[初回ログイン](https://docs.gitlab.com/install/next_steps/)、[登録制限](https://docs.gitlab.com/administration/settings/sign_up_restrictions/)、[ユーザー作成](https://docs.gitlab.com/user/profile/account/create_accounts/)

## 7. メール送信を設定する

メール確認・パスワード復旧・招待・通知のため、送信できるSMTPを用意します。既存のSMTPサービスのホスト名・ポート・認証方式・送信元制限を確認し、`/etc/gitlab/gitlab.rb` に設定します。

次は、587番でSTARTTLSとログイン認証を使うサービスの例です。接続先の指定に合わせて変更し、例の値をそのまま使わないでください。

```ruby
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = 'smtp.example.invalid'
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = 'YOUR_SMTP_USER'
gitlab_rails['smtp_password'] = 'YOUR_SMTP_PASSWORD'
gitlab_rails['smtp_domain'] = 'YOUR_MAIL_DOMAIN'
gitlab_rails['smtp_authentication'] = 'login'
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false
gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
gitlab_rails['gitlab_email_from'] = 'gitlab@example.invalid'
```

```sh
sudo gitlab-ctl reconfigure
```

設定ファイルには実際のSMTP認証情報が入るため、rootだけが読める権限を維持し、教材やリポジトリへコピーしません。公式SMTP文書のテスト送信を行い、通常ユーザーの確認メール・パスワード復旧メールも受信できることを確認します。

参考：[SMTPの設定とテスト](https://docs.gitlab.com/omnibus/settings/smtp/)

## 8. 一般ユーザーでプロジェクトを動かす

1. 管理者がユーザー一覧から新しい一般ユーザーを作成し、必要なグループ・プロジェクトの権限だけを与えます。
2. 利用者が受信したサインイン用メールからパスワードを設定し、自前GitLabへログインします。
3. [利用者の初期設定](account.md)のメール・2FA・SSH鍵設定を、自前の接続先で行います。GitLab.comへの鍵登録だけでは、このサーバーへ登録したことになりません。管理者側も、メール・2FA・復旧方法の確認を済ませます。
4. [Privateプロジェクト](README.md#1-最初のprivateプロジェクトを作る)をREADME付きで作ります。
5. その画面のURLでcloneし、作業用ブランチで編集・コミットしてpushします。
6. Merge Requestを作成し、権限のあるユーザーが取り込めることを確認します。

OpenSSHで接続するなら、クライアントの `.ssh/config` に、自前サーバー用の未使用の別名を追加できます。次のホスト名も実際の接続先へ置き換え、鍵は自前GitLabへ公開鍵登録したものに合わせます。

```sshconfig
Host gitlab-self
    HostName gitlab.example.invalid
    User git
    IdentityFile ~/.ssh/gitlab_tutorial_ed25519
    IdentitiesOnly yes
```

```sh
ssh -T gitlab-self
```

初回は、管理者または自前GitLabのインスタンス情報で確認したSSHホスト鍵のフィンガープリントと照合します。GitLab.comのホスト鍵とは別です。接続確認後は、たとえば `git@gitlab-self:NAMESPACE/PROJECT.git` のURLでこの別名を使えます。

SSH公開鍵はGitLabのWeb設定から登録します。OS側の `authorized_keys`、リポジトリの内部ディレクトリ、`git-shell` を手作業で前章の構成へ変えません。

GitLab RunnerはCI/CDジョブを実行する別の構成です。Web画面とGitの送受信が動いたことを確認してから、必要なら [Runnerの導入](https://docs.gitlab.com/runner/install/)へ進めます。

## 9. バックアップ・復元・更新

Linuxパッケージの基本コマンドです。

```sh
sudo gitlab-backup create
sudo gitlab-ctl backup-etc
```

| 対象 | 既定の保存先・扱い |
| --- | --- |
| アプリケーションのバックアップ | `/var/opt/gitlab/backups`。DB、リポジトリなどを保存する |
| `/etc/gitlab` の設定バックアップ | `/etc/gitlab/config_backup`。`gitlab.rb` と `gitlab-secrets.json` などを含む |
| TLS・SSHの鍵、外部オブジェクトストレージなど | アプリのバックアップだけで揃うとは限らないため、構成に合わせて別途保管する |

アプリのデータと秘密情報は、安全に分けて保管し、別ホストにも世代を分けて保存します。`gitlab-secrets.json` は暗号化されたデータや2FAなどの復元に必要です。単にGitのcloneやbundleを保存するだけでは、ユーザー・Issue・権限などを含むGitLab全体の復元にはなりません。

復元先には、バックアップを作ったときと**完全に同じバージョンとCE／EE種別**が必要です。空の検証環境で復元し、ログイン・送受信・メール・必要なデータが戻ることも確認します。稼働中の本番環境へ、試しに復元を重ねる手順にはしません。

更新前には、バックアップ、OS・コンポーネント要件、必要な停止時間とアップグレード経路を確認します。バージョンを大きく飛ばさず、公式が指定する必須の経由バージョンと、各段階のバックグラウンド移行完了を確認します。

参考：[データのバックアップ](https://docs.gitlab.com/administration/backup_restore/backup_gitlab/)、[設定のバックアップ](https://docs.gitlab.com/omnibus/settings/backups/)、[復元](https://docs.gitlab.com/administration/backup_restore/restore_gitlab/)、[更新経路](https://docs.gitlab.com/update/upgrade_paths/)

## 構築完了の確認

- HTTPSで正しいサーバーへ接続でき、サービス状態・診断結果に未解決の問題がない。
- rootのパスワード・メール・2FAと、利用者登録の方針を設定した。
- 一般ユーザーがメールを受信し、Privateプロジェクトでclone・push・Merge Requestを使える。
- バックアップを別の場所へ保管し、同じバージョンの検証環境で復元を確認した。
- 更新、容量監視、証明書更新、鍵の失効を誰が担当するか決めた。
