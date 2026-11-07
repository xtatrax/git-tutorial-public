# VS Code標準のGit機能を使う

[全体の案内へ戻る](../README.md) · [コマンドの実習](../git/README.md) · [GUI操作の基本](../old/README.md)

Visual Studio Codeのデスクトップ版には、Gitの標準機能が組み込まれています。この章では、ソース管理画面からclone・差分確認・ステージ・コミット・送受信・ブランチ操作を行います。Windows・macOS・Linuxで使う流れです。

操作名は **日本語表示（英語表示）** で併記しています。日本語はMicrosoftのJapanese Language Packの翻訳リソース（確認版 `1.131.2026090407`）と照合しました。VS Code本体・言語パックの版や画面によって、名前・省略記号・メニューの位置が異なる場合があります。公式資料の確認日：2026年11月7日。

## 1. VS CodeとGitを用意する

- [VS Code](https://code.visualstudio.com/download)をインストールする。
- [Git本体](../README2.md#gitのインストール)もインストールする。VS Codeの標準機能は、このGitを使って操作する。
- GitHubやGitLabへ接続する場合は、そのサービスの [アカウントと認証](#githubやgitlabとの接続)を用意する。

VS Codeの「ターミナル」から新しいターミナルを開き、確認します。

```sh
git --version
```

Gitを入れた直後に検出されない場合は、VS Codeを開き直します。Remote SSHやWSLなど別環境で作業している場合は、Gitを実行している環境と設定先も確認してください。

### 日本語表示にする場合

1. 拡張機能で [Japanese Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-ja)（拡張機能ID：`MS-CEINTL.vscode-language-pack-ja`）をインストールします。
2. コマンドパレットで **表示言語を構成する（Configure Display Language）** を開き、`日本語` / `ja` を選びます。
3. 再起動を求められたら、編集中のファイルを保存してから再起動します。

この設定はVS CodeのUI言語です。ターミナルで打つ `git status` などのコマンド名は英語のままです。英語の案内と見比べられるよう、以降も両方の表示を記載しています。

参考：[公式の言語パック案内](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-ja)、[Microsoftの日本語翻訳リソース](https://github.com/microsoft/vscode-loc/tree/main/i18n/vscode-language-pack-ja/translations)

よく使う入口は次のとおりです。

| 入口 | 開くもの |
| --- | --- |
| 左側のソース管理アイコン | ソース管理（Source Control）。変更一覧とコミット操作 |
| コマンドパレット | `Git: クローン`（Git: Clone） などのGit操作を名前で探す |
| 左下のブランチ名 | 現在のブランチ確認・切り替え |
| 統合ターミナル | 同じリポジトリでGitコマンドを実行する |

コマンドパレットは、Windows・Linuxでは `Ctrl+Shift+P`、macOSでは `Cmd+Shift+P` で開きます。

参考：[標準Git機能の概要](https://code.visualstudio.com/docs/sourcecontrol/overview)

## 2. 作業するリポジトリを開く

### すでに手元にある場合

「ファイル → フォルダーを開く」（File → Open Folder...）で、対象リポジトリのルートを開きます。TortoiseGitやターミナルで作ったリポジトリも、同じフォルダーを開けば使えます。履歴をVS Codeへ取り込む別作業は不要です。

複数のリポジトリを開いている場合は、ソース管理の対象と、ターミナルの現在位置を確認してから操作します。

### 共有先からcloneする場合

1. 対象リポジトリの画面で接続URLをコピーします。
2. コマンドパレットで `Git: クローン`（Git: Clone） を選びます。ソース管理の `リポジトリの複製`（Clone Repository） からも始められます。
3. URLを入力し、保存先の親フォルダーを選びます。
4. 完了後、cloneしたフォルダーを開きます。

HTTPS・SSHのどちらかと、接続先に合う認証を使います。最初の練習は、READMEなどのコミットがすでにあるリポジトリで進めると分かりやすくなります。

### 手元で新しく始める場合

新しい練習用フォルダーを開き、ソース管理の `リポジトリの初期化`（Initialize Repository）、またはコマンドパレットの `Git: リポジトリの初期化`（Git: Initialize Repository） を選びます。既存リポジトリの中や、別のプロジェクトへ誤って初期化しないようにします。

この操作ではローカルリポジトリを作ります。GitHubなどに新しいリポジトリが作られるわけではありません。初期ブランチ名は設定によるため、左下に表示される実際の名前を確認します。

参考：[リポジトリの開始とclone](https://code.visualstudio.com/docs/sourcecontrol/repos-remotes)

## 3. コミットに記録する名義を設定する

作成・cloneしたリポジトリの統合ターミナルで、最初のコミット前に設定します。

```sh
git config --local user.name "Sample Contributor"
git config --local user.email "contributor@example.invalid"
git config --get user.name
git config --get user.email
```

これはローカル実習用の架空の名義です。実際に共有する場合は、公開してよい名前とアドレスへ置き換えます。実メールを公開しない場合は、[GitHubのnoreply](../github/account.md#5-手元のgitの記録名義を設定する)や [GitLabの非公開コミットメール](../gitlab/account.md#4-手元のgitの記録名義を設定する)を、その接続先の設定からコピーして使います。

`--local` は今のリポジトリに適用されます。VS Codeへログインする情報とは別で、名前やメールを変えても、過去のコミットは書き換わりません。

## 4. 保存・ステージ・コミットを分けて操作する

| 操作 | 結果 |
| --- | --- |
| 保存 | エディターの内容を作業ファイルへ書き込む |
| ステージ | 次のコミットへ入れる内容を選ぶ |
| コミット | ステージした内容を手元の履歴へ記録する |
| push | 記録した履歴を共有先へ送る |

たとえば、練習用の `notes.md` を作って文章を保存したら、ソース管理を開きます。

### 差分を確認する

`変更`（Changes） は未ステージの変更、`ステージされている変更`（Staged Changes） はコミットへ入れる準備ができた変更です。ファイルを選ぶと差分を開けます。新規ファイルも内容を確認してください。

### 記録する変更を選ぶ

1. `変更`（Changes） で対象ファイルを選び、差分を確認します。
2. ファイル横の `+`、または `変更をステージ`（Stage Changes） を選びます。
3. `ステージされている変更`（Staged Changes） 側でも差分を開き、コミットに入る内容を確認します。

一部の行だけなら、差分エディターで対象を選択し、`選択した範囲をステージする`（Stage Selected Ranges） を使えます。ステージを取り消す `変更のステージング解除`（Unstage Changes） は、編集内容を残したまま対象から外す操作です。

ステージしたあとに同じファイルをさらに編集すると、両方の一覧に出ることがあります。ステージした時点の内容と、その後の追加編集が分かれているためです。後からの編集も入れるなら、差分を確認して追加でステージします。

### コミットする

1. `ステージされている変更`（Staged Changes） の対象を確認します。
2. 変更の目的が分かるメッセージを入力します。
3. `コミット`（Commit） を選びます。
4. ステージ済みの変更が一覧から消え、履歴へ追加されたことを確認します。

何もステージせずにCommitを押すと、設定によっては変更をまとめて追加する確認が出たり、自動で追加されたりします。この教材では、その操作に任せず、先に対象を明示してステージします。

コミットメッセージ専用のエディターが開いた場合は、メッセージを編集して完了操作を行います。画面を開いた時点でコミット済みとは判断せず、ソース管理と履歴で結果を確かめます。

参考：[ステージとコミット](https://code.visualstudio.com/docs/sourcecontrol/staging-commits)

## 5. 標準のグラフとタイムラインで履歴を見る

ソース管理の **ソース管理グラフ（Source Control Graph）** で、コミット・ブランチ・共有先との関係を確認できます。見出しが **グラフ（Graph）** と表示される場合もあります。現在のVS Codeに含まれる標準機能です。

- コミットを選び、変更したファイルと差分を調べる。
- 現在のブランチとリモート側の参照を確認する。
- まだ送っていない変更、取得した変更を確認する。

1つのファイルの履歴は、エクスプローラーの `タイムライン`（Timeline） からも見られます。ただしTimelineには、Gitのコミットに加えてローカルの保存履歴が表示される場合があります。保存履歴と、共有されるGitの履歴を区別します。

参考：[グラフ・Timeline・履歴](https://code.visualstudio.com/docs/sourcecontrol/history)

## 6. fetch・pull・pushする

cloneしたリポジトリなら、通常は共有先が `origin` として登録されています。ソース管理の `...` メニューやコマンドパレットから操作できます。

| 操作 | 何をするか |
| --- | --- |
| `Git: フェッチ`（Git: Fetch） | 共有先の履歴と参照を取得する。作業中のブランチへは統合しない |
| `Git: プル`（Git: Pull） | 履歴を取得し、現在のブランチへ統合する |
| `Git: プッシュ`（Git: Push） | 現在のブランチの履歴を共有先へ送る |
| `変更の同期`（Sync Changes） | pullしてからpushする。単なる表示更新ではない |

コマンドパレットでは `Git: 同期`（Git: Sync）が、同じGit同期操作の入口です。

最初は、未コミットの変更を整理し、Fetchとグラフで確認してから、Pull・Pushを個別に実行します。接続先が複数ある場合は `Git: 指定元からプル...`（Git: Pull from...） や `Git: プッシュ先...`（Git: Push to...） で対象を指定できます。

Syncでpullが競合すると、解決するまでpushへは進みません。Pullの統合方法は設定や選択で変わるため、CLI編の `git pull --ff-only` と常に同じとは考えないでください。

pushするブランチには、まだ送っていない過去のコミットも含まれます。送信前に、[ファイルと履歴の公開確認](../PRIVACY.md)を行います。

### ブランチの発行とGitHubへの公開

- リモート設定済みのリポジトリで `ブランチを発行...`（Publish Branch） を使うと、現在のブランチを共有先へ初めて送り、追跡先を設定できます。
- `GitHub に公開`（Publish to GitHub） は、GitHub上に新しいリポジトリを作り、履歴を送る機能です。公開範囲と送信する履歴を確認してから使います。
- すでに作ったGitHub・GitLab・自前サーバーへ接続する場合は、そのURLを使います。未登録なら `Git: リモートの追加...`（Git: Add Remote...） で未使用のリモート名を追加できます。この追加自体は履歴の送信ではありません。

この章では、既存の `origin` を別の接続先へ書き換える前提では進めません。表示された送り先・ブランチを確認し、必要な場合だけ新しい共有先を追加します。

参考：[リモート・送受信・公開](https://code.visualstudio.com/docs/sourcecontrol/repos-remotes)

## 7. ブランチで作業を分ける

1. 未コミットの変更を整理し、起点にしたいブランチへ切り替えます。
2. コマンドパレットで `Git: ブランチの作成...`（Git: Create Branch...） を選びます。
3. `topic/add-example` など、未使用の名前を入力します。
4. 作成後に新しいブランチへ切り替わったことを、左下で確認します。
5. 編集・ステージ・コミットを行い、必要ならそのブランチをpushします。

既存ブランチへの切り替えは、左下のブランチ名、または `Git: チェックアウト先...`（Git: Checkout to...） から選びます。ブランチ作成と切り替えの挙動は、TortoiseGitの作成時チェック項目とは異なります。

ローカルでマージする場合は、まず統合先のブランチへ切り替え、`Git: マージ...`（Git: Merge...） で取り込むブランチを選びます。共有プロジェクトでは、参加先のルールに従い、Pull Request・Merge Requestを通す方法も使います。

参考：[ブランチ・マージ・stash](https://code.visualstudio.com/docs/sourcecontrol/branches-worktrees)

## 8. 競合を解決する

通常のマージ・Pullで競合した場合の流れです。

1. ソース管理の `変更のマージ`（Merge Changes） で競合したファイルを確認します。
2. ファイルを開き、CurrentとIncomingがどの変更か確かめます。
3. 必要な内容を選択・編集し、競合マーカーを残さず保存します。
4. 差分を確認してステージします。
5. すべての競合を解決したら、マージ結果のコミットを作ります。

複雑な場合は、対象ファイルの **マージ エディターで解決（Resolve in Merge Editor）** などを選び、3方向のマージエディターを開けます。別の入口では **マージ エディターを開く（Open Merge Editor）** と表示されます。

| 英語の見出し | 確認したパックの日本語表示例 | 意味 |
| --- | --- | --- |
| Current | 現在のマシン | 現在側の変更。実行中のマージ・rebaseなどによって指す側が変わる |
| Incoming | 受信中 | 取り込む側の変更 |
| Result | 結果 | 保存する解決後の内容 |

翻訳には文脈に合いにくい表記もあるため、見出し名だけで判断せず、対象ブランチと内容を確認します。インラインの競合表示では **現在の変更（Current Change）**・**入力側の変更（Incoming Change）** という別の表記も使われます。

インラインの **両方の変更を取り込む（Accept Both Changes）** は両側を残す操作で、正しく動く結果になる保証ではありません。重複や処理の順序も確認します。3方向のマージエディターでは、同種の操作が **両方を取り込む（Accept Both）** などと表示されます。

マージエディターの `マージの完了`（Complete Merge） は、そのファイルをステージしてエディターを閉じる操作です。リポジトリ全体のマージ完了とは別なので、残りの競合とステージ済み差分を確認してからコミットします。

rebaseやcherry-pickの途中では、解決後に該当するcontinue操作が必要です。迷ったら統合ターミナルで `git status` を見て、停止中の処理と次の操作を確認します。取りやめる場合も、処理に対応するAbortを使い、必要な解決作業を先に保存します。

参考：[競合解決](https://code.visualstudio.com/docs/sourcecontrol/merge-conflicts)

## 9. 取り消し・無視設定を使う

| 目的 | 操作と注意点 |
| --- | --- |
| ステージから外す | `変更のステージング解除`（Unstage Changes）。編集内容は残る |
| 未ステージの編集を捨てる | `変更`（Changes） の対象で `変更を破棄`（Discard Changes）。未追跡ファイルでは削除になる場合もあるため、対象と確認表示をよく見る |
| すでにコミットした変更を打ち消す | [コミットの取り消し](../git/README.md#6-取り消しは対象を確かめてから)へ進み、共有済みの履歴を扱う方法を選ぶ |
| 管理しないファイルを決める | `.gitignore` を編集し、規則の差分も確認してコミットする |

DiscardはUnstageと違い、選んだ編集を捨てる操作です。必要な内容は別に保存してから実行します。`.gitignore` も、追跡済みのファイルや過去の履歴を自動で消すものではありません。

参考：[変更の取り消し](https://code.visualstudio.com/docs/sourcecontrol/staging-commits)

## GitHubやGitLabとの接続

標準のGit機能は、GitHub・GitLab・自前のSSHベアサーバーなどのリポジトリで使えます。接続先のURLと認証方式を合わせます。

- [GitHub](../github/account.md)：noreply、SSH鍵またはHTTPSの認証を準備する。
- [GitLab](../gitlab/account.md)：そのGitLabのメール設定・SSH鍵・トークンなどを準備する。
- [自前SSHサーバー](../server/README.md)：管理者が案内したURLと認証鍵を使う。

GitHubのサインイン操作と、コミットに記録する名前・メールは別です。SSHの鍵や設定が違えば、ブラウザーへログイン済みでもGitの接続は失敗します。

この標準編では、Pull Request・Merge Requestの作成やレビューは、それぞれのブラウザー画面で行います。VS Code内でGitHubのPRやIssueを管理する機能は、別の拡張機能を使う領域です。

同じPCのベアを共有先にする練習もできます。[ベア実習](../bare/README.md)の通常の作業用クローンをVS Codeで開けば、GitHubアカウントなしで差分・コミット・push／pullを試せます。

## うまく動かないとき

| 状況 | 確認すること |
| --- | --- |
| Gitが検出されない | Gitのインストール、VS Codeの再起動、実行環境、組み込みGit機能の有効状態 |
| 別のフォルダーの変更が出る | 開いたルート、ソース管理で選択したリポジトリ、ターミナルの現在位置 |
| commitに意図しない変更が入る | ステージされている変更（Staged Changes）、部分ステージ、ステージ後の追加編集、Smart Commitなどの設定 |
| 認証できない | HTTPS／SSH、対象アカウント、鍵、認証ツール、WSLなどの環境の違い |
| pushが拒否される | 権限、保護ブランチ、共有先の更新、追跡先。forceで進めず原因を確認する |

コマンドパレットの `Git: Git 出力の表示`（Git: Show Git Output） で、VS Codeが実行したGit操作とエラーを確認できます。ログを他の人へ渡す場合は、パス・接続先・メール・認証情報を確認してから共有してください。

参考：[Gitのトラブル対応](https://code.visualstudio.com/docs/sourcecontrol/troubleshooting)
