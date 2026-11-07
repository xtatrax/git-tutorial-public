# GitHub Desktopで使うGit（Windows・macOS）

[全体の案内へ戻る](../README.md) · [GitHubの初期設定](../github/account.md) · [コマンドの実習](../git/README.md)

GitHub Desktopは、差分の確認、コミット、ブランチ、リモートとの送受信を画面から操作するGitクライアントです。この章では、作業用リポジトリを開き、編集を記録して共有する流れを説明します。

操作名は英語UIを目印にし、日本語は意味の説明として併記します。公式版の日本語UIや、日本語用の言語パックを前提にしていません。VS CodeのJapanese Language PackもGitHub Desktopには適用されません。大文字・小文字やメニューの表記はOS・バージョンで異なる場合があります。

公式資料の確認日：2026年11月7日。手順は公式資料に基づき、Windows・macOSの実機画面では未検証です。実際のアカウント・個人のファイルパスが写る画像は使いません。

参考：[GitHub Desktopの概要と始め方](https://docs.github.com/en/desktop/overview/getting-started-with-github-desktop)、[表示言語についての公式プロジェクトでの回答](https://github.com/desktop/desktop/issues/21826#issuecomment-4158448528)

## 1. インストールとサインイン

[公式ダウンロードページ](https://desktop.github.com/)から、自分のPCに合うインストーラーを取得します。確認時点の公式対応OSは次のとおりです。

| OS | 公式版の対応 |
| --- | --- |
| Windows | Windows 10以降、64bit |
| macOS | macOS 12以降 |
| Linux | 公式版の対応対象外。Linuxでの練習は [VS Code標準のGit機能](../vscode/README.md)や [CLI](../git/README.md)を使う |

Desktop内部で使うGitは同梱されています。コマンドラインやVS CodeでもGitを使う場合は、そちらで使うGitを [別途準備](../README2.md)し、`git --version` で確認します。

GitHubへ接続する場合は、[アカウント作成と初期設定](../github/account.md)を済ませます。Desktopの設定を開く入口はOSで異なります。

| OS | 設定を開く操作 |
| --- | --- |
| Windows | `File → Options...`（ファイル → オプション） |
| macOS | `GitHub Desktop → Settings...`（アプリの設定） |

`Accounts`（アカウント）で `Sign Into GitHub.com` を選び、`Continue With Browser` からブラウザーで認証してDesktopへ戻ります。認証情報をコミットメッセージやリモートURLへ書きません。

GitHubとの接続はHTTPSを基本に進めます。SSHで取得済みのリポジトリでは、Desktopへのサインインだけで接続できるとは限りません。まず現在のURLと認証方式を確認し、エラーが出ても既存の接続先を一律に書き換えないようにします。

初回起動中にGitの名前・メールの設定を求められた場合も、次の章の適用範囲を確認してから保存してください。

参考：[対応OS](https://docs.github.com/en/desktop/overview/supported-operating-systems-for-github-desktop)、[インストール](https://docs.github.com/en/desktop/installing-and-authenticating-to-github-desktop/installing-github-desktop)、[サインイン](https://docs.github.com/en/desktop/installing-and-authenticating-to-github-desktop/authenticating-to-github-in-github-desktop)、[接続方式](https://docs.github.com/en/desktop/installing-and-authenticating-to-github-desktop/about-connections-to-github-in-github-desktop)、[同梱Gitについての公式回答](https://github.com/desktop/desktop/issues/15160#issuecomment-1225386551)

## 2. コミットの名義と設定先を区別する

GitHubのサインイン情報と、コミットに記録する名前・メールは別です。特に、次の2つの入口を区別します。

| 設定する入口 | 保存先・影響 |
| --- | --- |
| アプリの `Options / Settings → Git` | global Git設定。ほかのリポジトリでも使われる既定の名義を変える |
| `Repository → Repository Settings... → Git Config` | 選択中のリポジトリについて、globalの名義を使うか、localで上書きするか選ぶ |

今のリポジトリだけ別の名義にする場合は、次の章で追加・cloneしたあと、最初のコミット前に設定します。

1. `Current Repository`（現在のリポジトリ）で対象を確認します。
2. `Repository → Repository Settings...` を開き、`Git Config` を選びます。
3. `Use a local Git config`（このリポジトリ用のGit設定）を選びます。
4. `Name` と `Email` に公開してよい名義を指定し、`Save` で保存します。

ローカル実習用なら `Sample Contributor`、`contributor@example.invalid` などの架空の名義を使えます。GitHubのアカウントと関連付ける場合は、確認済みのアドレスか、[自分用のnoreplyアドレス](../github/account.md#2-メール確認と非公開設定)をGitHubの設定画面からコピーします。

設定を変えても、過去のコミットに記録された名義は変わりません。すでに正しい名義が設定されている場合は、値と適用範囲を確認してそのまま使えます。

参考：[global・リポジトリ別のGit設定](https://docs.github.com/en/desktop/configuring-and-customizing-github-desktop/configuring-git-for-github-desktop)

## 3. リポジトリを開く・cloneする・新しく作る

### 手元にあるリポジトリを追加する

1. `File → Add Local Repository...`（既存のローカルリポジトリを追加）を選びます。
2. `Choose...` で、通常の作業用リポジトリのフォルダーを選びます。
3. `Add Repository` を押し、`Current Repository` と表示されたファイルを確認します。

CLIやVS Codeで使っていたフォルダーも、そのまま追加できます。既存の履歴を引き継いで操作するため、追加のために `.git` を削除したり、リポジトリを初期化し直したりする必要はありません。

この追加はDesktopの一覧へ登録する操作です。GitHub上に新しいリポジトリを作る操作ではありません。

参考：[ローカルリポジトリの追加](https://docs.github.com/en/desktop/adding-and-cloning-repositories/adding-a-repository-from-your-local-computer-to-github-desktop)

### GitHubなどからcloneする

1. `File → Clone Repository...`（リポジトリを複製）を選びます。
2. GitHubの一覧から対象を選ぶか、`URL` タブへ接続URLを入力します。
3. `Local Path` で作業用の保存先を確認します。ほかのプロジェクトへ重ねないようにします。
4. `Clone` を押し、完了後に対象リポジトリとブランチを確認します。

GitHubのHTTPS URLの例は `https://github.com/OWNER/REPOSITORY.git` です。`OWNER` と `REPOSITORY` は自分の接続先に置き換えます。最初はREADMEなどのコミットがある練習用リポジトリを使うと進めやすくなります。

参考：[cloneとfork](https://docs.github.com/en/desktop/adding-and-cloning-repositories/cloning-and-forking-repositories-from-github-desktop)

### 新しいローカルリポジトリを作る

新しく始める場合は `File → New Repository...` を使います。`Name`、`Local Path`、README・無視設定・ライセンスなどを確認して `Create Repository` を選びます。通常は `Local Path` で選んだ場所の中に、`Name` のフォルダーが作られます。

作成時にREADMEなどの初期コミットが作られる場合があるため、作成前に現在のGitの名義も確認します。全体の名義を変更せず、初回からリポジトリ別の名義を指定したい場合は、[CLIで初期化とlocal設定](../git/README.md#1-練習用リポジトリを作る)を済ませてからDesktopへ追加する方法もあります。

この段階ではローカルの作成です。GitHubへ送る操作は [リポジトリの公開](#8-publish-repositoryとpublish-branch)で分けて扱います。

参考：[最初のリポジトリの作成](https://docs.github.com/en/desktop/overview/creating-your-first-repository-using-github-desktop)

## 4. 画面の見方

| 表示 | 確認できるもの |
| --- | --- |
| `Current Repository` | いま操作するリポジトリ |
| `Current Branch` | コミット・送受信の対象になるブランチ |
| `Changes` | まだコミットしていない変更と、今回記録する対象 |
| `History` | 現在のブランチのコミットと、その差分 |
| `Fetch origin / Pull origin / Push origin` など | 共有先との状態に応じて変わる操作ボタン |

ファイルはエディターで編集して保存し、Desktopへ戻って差分を確認します。外部エディターはアプリの `Options / Settings → Integrations → External Editor` で選べます。開く入口は `Repository → Open in EDITOR` で、`EDITOR` の部分は選んだエディター名になります。

参考：[外部エディターの設定](https://docs.github.com/en/desktop/configuring-and-customizing-github-desktop/configuring-a-default-editor-in-github-desktop)

## 5. 差分を確認してコミットする

1. リポジトリとブランチを確認し、エディターで `notes.md` などを編集して保存します。
2. Desktopの `Changes`（変更）を開き、ファイルを選んで差分を読みます。
3. 今回記録するファイルだけにチェックを残します。
4. `Summary`（短い要約）に目的を書き、必要なら `Description`（詳しい説明）を補足します。
5. `Commit to BRANCH`（表示されたブランチへコミット）を押します。

たとえばSummaryは「練習メモにcloneの説明を追加」のように、何を変えたか分かる文章にします。`BRANCH` の部分が実際の対象ブランチです。

一部の変更だけを記録する場合は、差分の変更行をクリックして選択を切り替えます。青く選択された行がコミット対象で、外した変更は作業ファイルに残ります。ファイルのチェックを外す操作も、編集内容を捨てる操作ではありません。

Desktopでは、このチェック・行選択を確認してコミットします。CLIのステージ状態だけを見て対象を決めず、ほかのGitクライアントで同時に追加・コミットしないようにします。

コミットは手元の履歴への記録です。GitHubへ送信する `Push origin` は別の操作です。

参考：[変更の選択・部分コミット・コミットメッセージ](https://docs.github.com/en/desktop/making-changes-in-a-branch/committing-and-reviewing-changes-to-your-project-in-github-desktop)

## 6. 履歴で結果を確認する

`History`（履歴）を開き、いま作ったコミットを選びます。メッセージ、記録された名義、コミットID、含まれるファイルの差分を確認します。複数ファイルがある場合は、それぞれを選びます。

`Changes` が空でも、まだ送っていないコミットがある場合があります。また、一部だけコミットした場合は残りの編集が `Changes` に残ります。作業ファイルの状態と、共有先への送信状況を分けて確認しましょう。

参考：[ブランチの履歴を見る](https://docs.github.com/en/desktop/making-changes-in-a-branch/viewing-the-branch-history-in-github-desktop)

## 7. fetch・pull・pushする

共有先が `origin` の場合の、主なボタンです。状態によって表示が切り替わります。

| 操作 | 何をするか |
| --- | --- |
| `Fetch origin`（取得） | 共有先の履歴と参照を取得する。作業ブランチには統合しない |
| `Pull origin`（取得して統合） | 共有先の変更を現在のブランチへ取り込む。競合する場合がある |
| `Push origin`（送信） | ローカルの未送信コミットを共有先へ送る |

最初は未コミットの編集を整理し、`Fetch origin` で更新を確認します。取り込むコミットがあれば `Pull origin` で反映し、必要な確認を行ったうえで自分のコミットを `Push origin` で送ります。設定によっては `Pull origin with rebase` が表示されるため、通常のマージと同じ操作とは考えず、選んだ方式を確認します。

pushで送るのは、選択中のファイルだけではなく、そのブランチのまだ送っていない履歴です。送信前に [本文・ファイル・過去のコミット](../PRIVACY.md)も確認します。pushが拒否された場合は、権限、保護ブランチ、共有先の更新を調べ、force pushで押し通さないようにします。

参考：[ブランチの同期](https://docs.github.com/en/desktop/working-with-your-remote-repository-on-github-or-github-enterprise/syncing-your-branch-in-github-desktop)、[GitHubへpushする](https://docs.github.com/en/desktop/making-changes-in-a-branch/pushing-changes-to-github-from-github-desktop)

## 8. Publish repositoryとPublish branch

### 共有先のないローカルリポジトリをGitHubへ公開する

GitHubに新しいリポジトリを作って送りたいときは、`Publish repository` を使います。名前、説明、公開先のアカウント・組織と、`Keep this code private`（非公開にする）を確認して実行します。このチェックを外すと公開リポジトリになります。

「Publish」は外部へ履歴を送る操作です。履歴の名義や過去のファイルも確認してから進めます。すでに共有先があるリポジトリは、その接続先で送受信します。公開ボタンを出すために既存の `origin` を削除する手順にはしません。

参考：[ローカルのプロジェクトをGitHubへ公開する](https://docs.github.com/en/desktop/adding-and-cloning-repositories/adding-an-existing-project-to-github-using-github-desktop)

### 新しい作業ブランチを共有先へ送る

共有先はあるものの、現在のブランチがまだ送信されていない場合は `Publish branch`（ブランチを発行）を使います。既存リポジトリへブランチを送る操作で、新しいGitHubリポジトリを作る操作とは異なります。

参考：[ブランチの作成と発行](https://docs.github.com/en/desktop/making-changes-in-a-branch/managing-branches-in-github-desktop)

## 9. 作業ブランチとPull Request

1. 未コミットの編集を整理し、`Current Branch` から起点のブランチを選びます。
2. `New Branch` を選び、`topic/add-notes` などの名前を入力します。`Create branch based on...` の起点を確認し、`Create Branch` で作成します。
3. 新しいブランチへ切り替わったことを確認し、編集・コミットします。
4. 共有する場合は `Publish branch` または `Push origin` で送ります。
5. `Preview Pull Request` で差分と統合先のbaseブランチを確認します。
6. `Create Pull Request` からブラウザーへ進み、タイトル・説明・対象ブランチを確認してPRを作成します。

PRの作成はマージの完了ではありません。参加先のレビューやチェックのルールに従います。すでにPRがある場合は、そのPRを開く表示になります。

ブランチ切り替え時に未コミットの変更を持ち越すか、元のブランチ側へ退避するかを尋ねられたら、対象を読んで選びます。迷う場合は先に現在のブランチで作業を整理します。

参考：[ブランチの操作](https://docs.github.com/en/desktop/making-changes-in-a-branch/managing-branches-in-github-desktop)、[Pull Requestの作成](https://docs.github.com/en/desktop/working-with-your-remote-repository-on-github-or-github-enterprise/creating-an-issue-or-pull-request-from-github-desktop)

## 10. ローカルのマージと競合解決

ローカルで統合する練習では、先に統合先のブランチへ切り替えます。`Current Branch → Choose a branch to merge into BRANCH` から取り込むブランチを選び、`Merge 取り込み元 into 統合先` と表示される方向を確認して実行します。

Pullや通常のマージで競合した場合は、次の流れです。

1. Desktopに表示された競合ファイルを確認し、外部エディターで開きます。
2. 両側の変更を読み、残す内容へ編集して保存します。競合マーカーや重複を残さないようにします。
3. Desktopへ戻り、各ファイルが解決済みとして扱われていることと差分を確認します。
4. すべて解決したら `Continue merge`（マージを続けて完了）を押します。macOSでは `Continue Merge` などと表示されます。
5. 履歴で結果を確認し、必要な動作確認をしてから共有先へ送ります。

取りやめる入口は `Abort merge / Abort Merge` です。必要な解決作業は先に別途保存し、確認表示を読んでから実行します。rebaseなどの途中では、表示されている処理に対応するContinue・Abortを選びます。

参考：[ブランチを統合する](https://docs.github.com/en/desktop/working-with-your-remote-repository-on-github-or-github-enterprise/syncing-your-branch-in-github-desktop)、[競合時の表示・続行操作の公式実装](https://github.com/desktop/desktop/blob/development/app/src/ui/multi-commit-operation/base-multi-commit-operation.tsx)

## 11. チェックを外す・破棄する・コミットを取り消す

| 目的 | 操作と結果 |
| --- | --- |
| 今回のコミットから外す | `Changes` のチェック・選択行を外す。作業ファイルの編集は残る |
| 未コミットの編集を捨てる | 対象の右クリックから `Discard Changes` など。確認画面の対象を読んで実行する |
| まだpushしていない直前のコミットを取り消して編集に戻す | `Undo`。コミットが履歴から外れ、変更は作業側へ戻る。対象と未送信であることを確認する |
| コミットの変更を、後続コミットで打ち消す | `History` の対象を右クリックし `Revert Changes in Commit`。打ち消すための新しいコミットを作る |

Discardは、コミット対象から外す操作とは異なり、作業ファイルを書き換えます。必要な編集は別に保存してから使います。Undoで履歴を編集する操作と、新しい履歴を追加するRevertも区別してください。Revertで競合した場合は解決が必要です。

共有済みの履歴では、ほかの人への影響とプロジェクトのルールを確認します。この入門では、amend・reset・rebase・force pushを、通常のコミットの代わりに使いません。

参考：[変更の破棄](https://docs.github.com/en/desktop/making-changes-in-a-branch/committing-and-reviewing-changes-to-your-project-in-github-desktop#discarding-changes)、[コミットのUndo](https://docs.github.com/en/desktop/managing-commits/undoing-a-commit-in-github-desktop)、[コミットのRevert](https://docs.github.com/en/desktop/managing-commits/reverting-a-commit-in-github-desktop)

## GitLab・自前サーバー・ローカルのベアと使う

GitHub以外の通常の作業用リポジトリも、`Add Local Repository` で追加できます。接続先と認証方式は、そのサービス・サーバーの案内に合わせます。GitHubへのサインインが、別サービスへのログインを兼ねるわけではありません。

- [GitLab](../gitlab/account.md)：HTTPSのcloneには `Clone Repository → URL` を使う。Git Credential Managerを使う連携方法は、[公式プロジェクトのGitLab連携文書](https://github.com/desktop/desktop/blob/development/docs/integrations/gitlab.md)を参照する。
- [自前SSHサーバー](../server/README.md)：まずCLIで接続と通常の作業用cloneを準備する。Desktopでの送受信にはSSH認証も必要になる。
- [同じPCのベア](../bare/README.md)：`demo.git` 自体ではなく、そこから作った通常の作業用クローンをDesktopへ追加する。

## うまく動かないとき

| 状況 | 確認すること |
| --- | --- |
| 別のフォルダーの変更が見える | `Current Repository`、保存先、エディターで開いたフォルダー |
| 変更したファイルが出ない | 保存済みか、`.gitignore` に該当しないか、対象のリポジトリか |
| 名前・メールが想定と違う | リポジトリの `Git Config` とglobal設定、コミットに記録された実際の名義 |
| コミットできない | メッセージと対象選択、未解決の競合、Git hooksやプロジェクトのルール |
| clone・pushが認証で失敗する | HTTPS／SSH、対象アカウント、リポジトリ権限、組織の追加認証 |
| Publish repositoryが出ない | すでに共有先があるか。既存の `origin` を削除せず、現在の送り先を確認する |

エラーを共有するときは、アカウント名、接続URL、ローカルパス、メール、認証情報が含まれていないか確認します。

参考：[GitHub Desktopのトラブル対応](https://docs.github.com/en/desktop/troubleshooting-and-supporting-github-desktop)
