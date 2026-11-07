# TortoiseGitで使うGit（Windows）

[全体の案内へ戻る](../README.md) · [GUI操作の基本](../old/README.md) · [GitHubの初期設定](../github/account.md)

TortoiseGitは、Windowsのエクスプローラーの右クリックメニューからGitを操作するクライアントです。この章では、既存のリポジトリをcloneし、変更を記録して共有する流れを説明します。

操作名は公式マニュアルの英語表記を目印にしています。日本語化した画面では対応する項目を選んでください。メニューの位置はWindowsのバージョンや設定で変わるため、見当たらない場合は「その他のオプションを確認」などの従来の右クリックメニューも確認します。

公式資料の確認日：2026年11月7日。実際のアカウント・ファイルパスが写る画像は使っていません。

## 1. インストールと初期設定

Windows上に、次のものを用意します。

- コマンドラインのGit：通常は [Git for Windows](https://gitforwindows.org/)を使う。
- [TortoiseGit本体](https://tortoisegit.org/download/)：PCに対応するインストーラーを選ぶ。
- 日本語表示を使う場合は、同じダウンロードページのJapanese言語パックも、本体に対応するものを選んで本体のあとに入れる。

TortoiseGitだけでなく、`git.exe` を提供するGit本体も必要です。インストーラーが示すOS・追加ランタイムなどの条件も確認します。

右クリックから `TortoiseGit → Settings` を開き、`General` でGitの実行ファイルが正しく検出されているか確認します。日本語の言語パックを入れた場合は、同じ画面の言語設定で日本語を選べます。

GitHubを使う場合は、先に [アカウント作成と初期設定](../github/account.md)を済ませます。自分のリポジトリでの練習には、READMEがあるリポジトリを用意すると、空の履歴や初期ブランチ名の違いで迷いにくくなります。

参考：[インストール](https://tortoisegit.org/docs/tortoisegit/tgit-intro-install.html)、[設定](https://tortoisegit.org/docs/tortoisegit/tgit-dug-settings.html)

## 2. cloneして作業場所を作る

1. GitHubなどで、リポジトリの接続URLをコピーします。
2. エクスプローラーで保存先の親フォルダーを開き、右クリックして `Git Clone...` を選びます。
3. `URL` に接続先、`Directory` に新しく作る作業フォルダーを指定します。ほかのプロジェクトや既存の作業フォルダーへ重ねないようにします。
4. 編集用のcloneでは `Clone into Bare Repo` と `No Checkout` は選びません。
5. 内容を確認して実行し、完了したら作業フォルダーを開きます。

接続先の例です。`OWNER` と `REPOSITORY` は、自分の接続先へ置き換えます。

| 接続先 | URL欄へ入れるもの |
| --- | --- |
| GitHub・HTTPS | `https://github.com/OWNER/REPOSITORY.git` |
| GitHub・SSH | `git@github.com:OWNER/REPOSITORY.git` |
| 同じPCのベア | `demo.git` の実際の絶対パス。参照ボタンでも選べる |

GitHubの公開リポジトリは、HTTPSなら認証なしでcloneできる場合があります。書き込みや非公開リポジトリの読み取りには認証が必要です。HTTPSではGit Credential Managerなど、Git for Windows側で設定した認証ツールの案内に従います。GitHubのログイン用パスワードを、GitのHTTPSパスワードとしては使いません。

SSHで使う場合は、先に [この章のSSH設定](#sshを使う場合)も確認してください。

「ここにリポジトリを作成」は新規の初期化で、既存リポジトリを取得するcloneとは別の操作です。

参考：[clone](https://tortoisegit.org/docs/tortoisegit/tgit-dug-clone.html)、[GitHubの認証](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)

## 3. コミットの名義を確認する

最初のコミットを作る前に、cloneした作業フォルダーで設定します。

1. 作業フォルダーを右クリックし、`TortoiseGit → Settings` を開きます。
2. `Git` の設定ページで `Local` を選びます。これは現在のリポジトリの `.git/config` に保存する設定です。
3. `Name` に公開してよい名義、`Email` にコミット用アドレスを入力し、保存先がLocalであることを確認して適用します。
4. `Effective` などの有効な設定の表示で、実際に使われる値を確認します。

GitHubで実メールを公開したくない場合は、[GitHubのメール設定](../github/account.md#2-メール確認と非公開設定)からコピーした、自分用のnoreplyアドレスを使います。ログイン名・パスワードをここへ入力するのではありません。

`Global` はほかのリポジトリにも影響します。共有される `Project` の設定ファイルへ、私用メールなどを書かないようにします。

設定を変更しても、すでにあるコミットの名前やメールアドレスは変わりません。

## 4. 編集内容を確認してコミットする

ここからは、通常の作業フォルダーで操作します。初めは `main` のある練習用リポジトリを使い、共有プロジェクトでは参加先のブランチ運用に従います。

1. エディターで `notes.md` などのファイルを作成・編集し、保存します。
2. 作業フォルダーのルートを右クリックし、`TortoiseGit → Check for Modifications...` で変更一覧を開きます。
3. ファイルの差分を開き、変更内容を確認します。新規ファイルは、エディターでも中身を確認します。
4. ルートフォルダーから `Commit...` を開き、今回記録するファイルだけにチェックを入れます。
5. 差分と一覧をもう一度確認し、変更の目的が伝わるメッセージを入力してコミットします。

未追跡の新規ファイルも、コミット画面でチェックすると追加対象になります。一覧にない場合は、未追跡ファイルやプロジェクト全体を表示する設定を確認します。不要なログ・秘密鍵・ローカル設定へチェックを入れないでください。

TortoiseGitのコミット画面は、対象選択と追加・コミットをまとめて行えます。CLIで事前にステージした内容だけをそのまま確定する画面として扱わず、チェックされたファイルと内容を確認します。サブフォルダーから開いても、ほかの場所にあるステージ済みファイルが表示される場合があります。

コミットが成功しても、まだ手元の履歴に記録されただけです。GitHubやサーバーへ送る操作は次のpushです。

参考：[変更の確認](https://tortoisegit.org/docs/tortoisegit/tgit-dug-wcstatus.html)、[コミット](https://tortoisegit.org/docs/tortoisegit/tgit-dug-commit.html)

## 5. ログで記録を確認する

`TortoiseGit → Show log...` を開きます。

- 上の一覧で、コミットのメッセージ・名義・日時と現在のブランチを確認する。
- コミットを選び、下の変更ファイル一覧から差分を確認する。
- 意図しないファイルや個人情報が入っていないか、push前に確認する。

フォルダーのアイコンは状態の目安です。更新の遅れや表示枠の制限もあるため、アイコンの色だけで判断せず、変更一覧とログを使いましょう。

参考：[ログ](https://tortoisegit.org/docs/tortoisegit/tgit-dug-showlog.html)

## 6. pushして共有先へ送る

1. 作業フォルダーで `TortoiseGit → Push...` を開きます。
2. 送り先のリモートを確認します。cloneした場合は通常 `origin` です。
3. ローカル側のブランチとリモート側のブランチを確認します。練習用の `main` を送るなら、両方が `main` です。
4. 初回に追跡先を設定する場合は `Set upstream/track remote branch` を選びます。
5. force系の指定をせずに実行し、送信結果を確認します。

pushするブランチに含まれる、まだ送っていない履歴も送信対象です。直前の1コミットだけを送る機能ではありません。実行前には [履歴も含む公開用データの確認](../PRIVACY.md)を行います。

権限や認証で拒否された場合は接続先・アカウント・認証方法を確認します。non-fast-forwardで拒否された場合は、他の変更を取得して履歴を確認してから統合します。

参考：[push](https://tortoisegit.org/docs/tortoisegit/tgit-dug-push.html)

## 7. fetch・pullで変更を取り込む

1. `Check for Modifications...` で作業中の変更を確認し、必要な変更をコミットするなどして整理します。
2. `Switch/Checkout...` やログで、更新したいローカルブランチが現在のブランチか確認します。
3. `TortoiseGit → Fetch...` で `origin` などの共有先から履歴を取得します。
4. ログでリモート側のブランチも表示し、現在のブランチとの関係を確認します。
5. 取り込む変更を確認できたら `TortoiseGit → Pull...` を開き、共有先と取り込むブランチを確認して実行します。

fetchは取得だけで、作業中のファイルへは統合しません。pullは取得して現在のブランチへ統合します。rebaseを選ぶと統合方法が変わるので、この章では通常のマージを使う設定で進めます。

CLI編の `git pull --ff-only` と、GUIの通常のPullが同じ設定とは限りません。履歴が分岐している場合は、どの変更をどう統合するか確認します。競合が出た場合は、次の解決手順へ進みます。

`Sync` はfetch・pull・pushなどをまとめて扱う画面です。最初は各操作を個別に選ぶと、何が行われたか確かめやすくなります。

参考：[fetchとpull](https://tortoisegit.org/docs/tortoisegit/tgit-dug-pull.html)

## 8. ブランチを分けて作業する

未コミットの変更がない状態で、作業の起点にするローカルブランチへ切り替えてから始めます。

1. `TortoiseGit → Create Branch...` を開きます。
2. `topic/add-example` など、未使用のブランチ名を入力します。
3. 起点が意図したコミット・ブランチになっているか確認します。
4. 作ったブランチで作業する場合は `Switch to new branch/tag` を選んで作成します。
5. ログなどで現在のブランチを確認してから編集・コミットします。

作成時に切り替えなかった場合は、`TortoiseGit → Switch/Checkout...` で作成済みのローカルブランチを選びます。未コミット変更を上書きするforceの指定は使いません。

ブランチを初めてpushするときは、送り元・送り先をそのブランチ名で確認します。GitHubならpush後にPull Requestを作る流れへ進めます。

### ローカルでマージする場合

1. 作業中の変更を整理してから、統合先のローカルブランチへ切り替えます。たとえば `main` です。
2. `TortoiseGit → Merge...` を開き、取り込むブランチとして `topic/add-example` を選びます。
3. 対象を確認して実行し、結果をログと差分で確認します。

`Squash`、`No Commit` などの追加オプションを選ぶと結果が変わります。この練習では選びません。fast-forwardで済む場合は、新しいマージコミットが作られないこともあります。

参考：[ブランチ作成](https://tortoisegit.org/docs/tortoisegit/tgit-dug-branchtag.html)、[切り替え](https://tortoisegit.org/docs/tortoisegit/tgit-dug-checkout.html)、[マージ](https://tortoisegit.org/docs/tortoisegit/tgit-dug-merge.html)

## 9. マージで競合したとき

1. 競合したファイルを一覧で確認します。
2. `Edit Conflicts` またはエディターで開き、両方の変更を見て残す内容を整理します。
3. 完成したファイルを保存し、意図した内容になっているか確認します。
4. `Resolved`、または `Resolve...` の対象選択で、確認済みのファイルを解決済みにします。
5. すべての競合が解決したら、差分を確認してマージ結果をコミットします。

「解決済み」は現在の内容をGitへ登録する操作です。押すだけで内容が正しく修正されるわけではありません。`mine` と `theirs` を選ぶ場合も、どちらのブランチの内容か確認します。

これはマージ・通常のpullの競合手順です。rebaseやcherry-pickの途中で競合した場合は、解決後にそれぞれの専用ダイアログで処理を継続します。

参考：[競合解決](https://tortoisegit.org/docs/tortoisegit/tgit-dug-conflicts.html)

## 10. 取り消しと無視設定

| 目的 | 操作と注意点 |
| --- | --- |
| ファイルの未コミット変更を捨てる | ファイルや親フォルダーの `Revert`。対象の編集が失われるので、必要なら先に別の場所へ保存する |
| コミットの変更を打ち消す | `Show log...` の対象コミットから `Revert change by this commit`。結果を確認し、未コミットならコミットして履歴へ記録する |
| 未追跡ファイルを共有の無視設定へ加える | `Add to Ignore List` から `.gitignore` へ追加し、その規則を確認してコミットする |
| 自分の環境だけで無視する | ローカル用の `.git/info/exclude` を使う |

ファイルに対するTortoiseGitの `Revert` と、コミットを打ち消すCLIの `git revert` は別の操作です。取り消す前に、どちらを対象としているか確かめます。

`.gitignore` へ追加しても、すでに追跡中のファイルや過去の履歴からは消えません。履歴を戻すResetやforce pushは、この基本手順には含めません。

参考：[取り消し](https://tortoisegit.org/docs/tortoisegit/tgit-dug-revert.html)、[無視設定](https://tortoisegit.org/docs/tortoisegit/tgit-dug-ignore.html)

## 同じPCのベアリポジトリで試す

[ローカルのベア実習](../bare/README.md)で作った `demo.git` は、TortoiseGitでも共有先として使えます。

1. `Git Clone...` のURLにベアのパスを指定し、編集用の通常のクローンを2つ作ります。すでにある作業フォルダーとは別の場所を使います。
2. それぞれのコミット名義を確認します。
3. 片方で編集・コミットし、`Push...` で `main` を送ります。
4. もう片方で `Fetch...`、ログの確認、`Pull...` を行い、更新されたファイルを確認します。

GitHubアカウントやネットワーク認証は不要です。作業用ファイルを編集するのはクローン側で、`demo.git` の中ではありません。空のベアから始めるときは、[初期ブランチの扱い](../bare/README.md)も確認してください。

## SSHを使う場合

TortoiseGitの `Settings → Network` で、実際に使うSSHクライアントを確認します。

| SSHクライアント | 鍵と接続設定 |
| --- | --- |
| OpenSSH | OpenSSH形式の鍵と `.ssh/config` を使う。前の章の設定を使う場合は、この方式にそろえる |
| TortoiseGitPlink・PuTTY系 | PuTTYの鍵・セッション、必要ならPageantを使う。OpenSSHの `.ssh/config` とは別 |

OpenSSHを使うなら、Git for Windowsに含まれる `ssh.exe` をSSHクライアントとして指定します。既存のSSH利用にも影響する設定なので、すでに動いている構成がある場合は先に確認します。

前の章の `git_tutorial_ed25519` は標準名ではないため、OpenSSHを選ぶだけではその鍵が自動で選ばれるとは限りません。Git for Windowsが使うホームディレクトリの `.ssh/config` を開き、既存の設定を残して、たとえば次の別名を追加します。同名のHost設定がすでにある場合は内容を確認し、別の未使用名を選びます。

```sshconfig
Host github-tutorial
    HostName github.com
    User git
    IdentityFile ~/.ssh/git_tutorial_ed25519
    IdentitiesOnly yes
```

この例では、[GitHubのSSH鍵登録](../github/account.md#6-手元のgitからの接続を用意する)が済んでいることを前提とします。cloneのURLも `git@github-tutorial:OWNER/REPOSITORY.git` にして、この別名の設定を使います。実際の接続先はGitHubで、`github-tutorial` は手元の設定に付けた名前です。`Load Putty Key` はOpenSSH用の鍵指定には使いません。

保存するファイル名は `config` で、`config.txt` にならないようにします。自前サーバーでは、[サーバー編](../server/README.md)の接続先別名も、同じOpenSSH方式で使えます。

PuTTY系を使う場合は、対応する秘密鍵形式や鍵読み込みを設定します。OpenSSHの秘密鍵を、clone画面の `Load Putty Key` へそのまま指定する手順とは分けてください。接続先へ登録するのは、どちらの方式でも公開鍵です。

最初の接続では、ホスト鍵のフィンガープリントを接続先の公式情報や管理者の案内と照合します。Git Bashで成功するのにTortoiseGitで失敗する場合は、SSHクライアント・鍵・ホームディレクトリ・接続設定が一致しているか確認します。

参考：[Network設定](https://tortoisegit.org/docs/tortoisegit/tgit-dug-settings.html)、[SSHとPuTTY](https://tortoisegit.org/docs/tortoisegit/tgit-ssh-howto.html)、[FAQ](https://tortoisegit.org/support/faq/)

## 操作の対応表

| したいこと | TortoiseGit | Gitの考え方・コマンド |
| --- | --- | --- |
| 複製して作業を始める | Git Clone | `git clone` |
| 変更を調べる | Check for Modifications、差分表示 | `git status`、`git diff` |
| 変更を選んで記録する | Commit画面で対象選択 | 追加・ステージと `git commit` |
| 履歴を調べる | Show log | `git log`、`git show` |
| 履歴を送る | Push | `git push` |
| 履歴だけ取得する | Fetch | `git fetch` |
| 取得して統合する | Pull | `git pull`。統合方法は選択・設定による |
| ブランチを作成・切り替え | Create Branch、Switch/Checkout | `git branch`、`git switch` など |

詳しい操作や表示の違いは、[TortoiseGit公式マニュアル](https://tortoisegit.org/docs/tortoisegit/)で確認できます。
