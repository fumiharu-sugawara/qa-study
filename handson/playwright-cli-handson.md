# playwright-cli ハンズオン勉強会

> 所要時間：約3〜4時間（休憩込み）
> 前提知識：コマンドラインの基本操作（`cd`、`ls`程度）

---

## はじめに：playwright-cli とは？

**playwright-cli** は「コマンド1行でブラウザを操作できる」ツールです。

### 前回学んだ `@playwright/test` との違い

| | @playwright/test | playwright-cli |
|---|---|---|
| 何をするもの？ | 自動テストを**書いて実行**するフレームワーク | ブラウザを**コマンド1行ずつ直接操作**するCLIツール |
| 使い方のイメージ | レシピ（テストコード）を作って一括実行 | 料理しながら都度指示を出す |
| 向いている場面 | 繰り返し実行するリグレッションテスト | 手動確認・調査・AIエージェントによる操作 |

### playwright-cli を使う場面のイメージ

```
ターミナルで 1行ずつ指示

playwright-cli open https://example.com  ← ブラウザが開く
playwright-cli type "検索キーワード"      ← 文字を入力
playwright-cli press Enter               ← Enterキーを押す
playwright-cli screenshot                ← 画面を保存
```

> まるで「ブラウザに話しかけて動かす」感覚です

---

## 環境チェック（事前準備）

```bash
node -v
# → v18.x.x 以上が表示されればOK
# PlaywrightはJavascript製なのでnodeが必要
```

---

## Chapter 1：インストールと確認（15分）

### 1-1. playwright-cli をインストールする

```bash
npm install -g @playwright/cli@latest
```

### 1-2. インストールを確認する

```bash
playwright-cli --help
```

コマンド一覧が表示されればインストール成功です 🎉

### 1-3. スキルをインストールする（重要！）

```bash
playwright-cli install --skills
```

> **スキルとは？**
> Claude Code などのAIアシスタントが playwright-cli を使いこなすための「説明書」です。
> これを入れておくと、AIが自動的にコマンドの使い方を理解して適切に操作してくれます。
> 人間が使う場合も「`--help` を読む手間」が省けます。

---

## Chapter 2：ブラウザを開いてみよう（20分）

### 2-1. ブラウザをヘッドレスで開く（画面なし）

```bash
playwright-cli open https://demo.playwright.dev/todomvc
```

> **ヘッドレスとは？**
> 画面を表示せずにバックグラウンドでブラウザが起動している状態です。
> サーバー上での自動実行やAI操作に向いています。

ターミナルに現在のページ状態（スナップショット）が出力されます：

```
### Page
- Page URL: https://demo.playwright.dev/todomvc/
- Page Title: React • TodoMVC

### Snapshot
[Snapshot](.playwright-cli/page-xxxxx.yml)
```

### 2-2. ブラウザを表示しながら開く（--headed）

```bash
playwright-cli open https://demo.playwright.dev/todomvc --headed
```

ブラウザウィンドウが表示されます。以降のコマンドで操作できます。

### 2-3. 別のURLへ移動する

```bash
playwright-cli goto https://example.com
```

> `open` はブラウザを**起動**するコマンド
> `goto` はすでに開いているブラウザを**別のURLへ移動**するコマンド

---

## Chapter 3：ページを操作してみよう（30分）

まず、デモサイトをヘッドあり（見える状態）で開きます：

```bash
playwright-cli open https://demo.playwright.dev/todomvc --headed
```

### 3-1. 文字を入力する（type / fill）

```bash
# フォーカスされている入力欄に文字を入力
playwright-cli type "牛乳を買う"

# Enterキーを押す
playwright-cli press Enter
```

> **type と fill の違い：**
> - `type`：今フォーカスが当たっている場所に入力（人間がキーボードを叩く感覚）
> - `fill <ref> <text>`：要素を指定して入力（確実に狙った場所に入れたいとき）

追加でもう1件入力してみましょう：

```bash
playwright-cli type "パンを買う"
playwright-cli press Enter
playwright-cli type "卵を買う"
playwright-cli press Enter
```

### 3-2. スナップショットで要素の「ref」を調べる

要素をクリックしたり操作したりするには、その要素の **ref（参照ID）** が必要です。

```bash
playwright-cli snapshot
```

出力例：

```yaml
- role: listitem
  - role: checkbox [ref=e21]  ← これがref
  - text: 牛乳を買う
- role: listitem
  - role: checkbox [ref=e35]
  - text: パンを買う
```

> **ref とは？**
> 「どの要素を操作するか」を識別するためのID番号です。
> 人間が「3番目のチェックボックス」と言う代わりに、コンピューターは `e21` のような記号で識別します。

### 3-3. チェックボックスをクリックする（check / click）

```bash
# ref番号はsnapshotの結果に合わせて変更してください
playwright-cli check e21
```

「牛乳を買う」にチェックが入ります。

```bash
# クリック（汎用）
playwright-cli click e35

# ダブルクリック
playwright-cli dblclick e35
```

### 3-4. ホバーする

```bash
playwright-cli hover e21
```

> 削除ボタン（×）などホバーしないと現れないUIを確認したいときに使います。

### 3-5. ドロップダウンを選択する（select）

```bash
playwright-cli select <ref> "選択肢の値"
```

### 3-6. ページをリロード・戻る・進む

```bash
playwright-cli reload        # リロード
playwright-cli go-back       # ブラウザの「戻る」
playwright-cli go-forward    # ブラウザの「進む」
```

---

## Chapter 4：スクリーンショットとPDFを保存する（15分）

### 4-1. スクリーンショットを撮る

```bash
# ファイル名自動（タイムスタンプ付き）
playwright-cli screenshot

# ファイル名を指定して保存
playwright-cli screenshot --filename=todo-completed.png
```

### 4-2. 特定の要素だけ撮る

```bash
# snapshotで調べたrefを指定
playwright-cli screenshot e21
playwright-cli screenshot e21 --filename=checkbox-only.png
```

> UIの特定部分だけキャプチャして、バグ報告やデザイン確認に使えます！

### 4-3. PDFとして保存する

```bash
playwright-cli pdf
playwright-cli pdf --filename=todo-list.pdf
```

> ページ全体をPDFに変換して保存します。レポートや証跡として使えます。

---

## Chapter 5：セッションを管理する（20分）

**セッション**とはブラウザの「作業状態」のことです。

> **日常のたとえ：**
> ブラウザのタブを複数開くように、playwright-cli でも複数のブラウザセッションを使い分けられます。
> 「テスト用」「本番確認用」など、別々の状態で並行して作業できます。

### 5-1. 複数のセッションを使い分ける

```bash
# デフォルトセッションでサイトAを開く
playwright-cli open https://demo.playwright.dev/todomvc --headed

# 別名セッション「todo-app」でサイトBを開く（-s= でセッション名を指定）
playwright-cli -s=todo-app open https://example.com --headed
```

### 5-2. セッション一覧を確認する

```bash
playwright-cli list
```

出力例：

```
 SESSION    BROWSER   URL
 default    chromium  https://demo.playwright.dev/todomvc
 todo-app   chromium  https://example.com
```

### 5-3. セッションを永続化する（--persistent）

デフォルトでは、ブラウザを閉じると Cookie やログイン状態が消えます。
`--persistent` を付けると、次回起動時も状態が引き継がれます。

```bash
playwright-cli open https://example.com --persistent
```

> ログインが必要なサービスの確認作業などで便利です。

### 5-4. セッションを閉じる

```bash
playwright-cli close           # 現在のセッションを閉じる
playwright-cli close-all       # 全セッションを閉じる
playwright-cli kill-all        # 全ブラウザを強制終了（固まったとき用）
```

---

## Chapter 6：ビジュアルダッシュボードで監視する（20分）

### 6-1. ダッシュボードを起動する

複数のセッションをまず起動しておきます：

```bash
playwright-cli open https://demo.playwright.dev/todomvc --headed
playwright-cli -s=session2 open https://example.com --headed
```

次にダッシュボードを開きます：

```bash
playwright-cli show
```

ブラウザでダッシュボードが開きます。

### 6-2. ダッシュボードでできること

**セッショングリッド（一覧画面）：**
- 実行中の全セッションがライブプレビューで表示
- セッション名・現在のURL・ページタイトルが一目でわかる
- セッションをクリックすると詳細画面へ

**セッション詳細画面：**
- ブラウザのライブビューをリアルタイムで確認
- アドレスバーやナビゲーションボタンが使える
- ビューポート内をクリックすると**自分でマウス・キーボード操作を引き継げる**
- `Escape` キーで操作を戻す

> **使いどころ：**
> AIエージェントが自動操作しているのをリアルタイムで監視したり、
> うまくいかない箇所で人間が操作を引き継いだりできます。

### 6-3. UIレビュー・デザインフィードバックモード

```bash
playwright-cli show --annotate
```

デザイナーやディレクターがUIに直接コメントを付けられるモードです。

---

## Chapter 7：タブを操作する（15分）

ブラウザのタブを複数開いて切り替えられます。

```bash
# 現在のタブ一覧を確認
playwright-cli tab-list

# 新しいタブを開く（URLを指定することも可能）
playwright-cli tab-new
playwright-cli tab-new https://example.com

# タブを切り替える（tab-listで表示されるindex番号を使う）
playwright-cli tab-select 1

# タブを閉じる
playwright-cli tab-close 1
```

> **日常のたとえ：**
> ブラウザで複数タブを開いて切り替えるのとまったく同じ操作です。
> ただしコマンドで行います。

---

## Chapter 8：DevToolsを使う（30分）

ブラウザの「開発者ツール」相当の情報をコマンドで取得できます。

### 8-1. コンソールログを確認する

```bash
playwright-cli console
```

ページのJavaScriptエラーや `console.log` の出力が表示されます。

```bash
# エラーだけ表示（error / warn / info / log / debug）
playwright-cli console error
```

> **使いどころ：**
> 「ボタンを押したらエラーが出た」というバグ報告のとき、
> コンソールのエラー内容をエンジニアに共有できます。

### 8-2. ネットワークリクエストを確認する

```bash
# ページ読み込み以降の全リクエスト一覧
playwright-cli requests

# 特定のリクエストの詳細（番号を指定）
playwright-cli request 3
```

> **使いどころ：**
> APIが呼ばれているか、どんなデータが送受信されているかを確認できます。

### 8-3. ロケーターを生成する（generate-locator）

要素を指定するための「ロケーター」（@playwright/testで使うセレクター）を生成できます。

```bash
# refを指定してロケーターを生成
playwright-cli generate-locator e21
```

出力例：

```
getByRole('checkbox', { name: '牛乳を買う' })
```

> **使いどころ：**
> `@playwright/test` のテストコードに貼り付けて使えます！
> 「このチェックボックスをどう書けばいい？」が一発でわかります。

### 8-4. 要素をハイライト表示する

```bash
# 要素を黄色くハイライト
playwright-cli highlight e21

# カスタムスタイルでハイライト
playwright-cli highlight e21 --style="border: 3px solid red"

# ハイライトを消す
playwright-cli highlight e21 --hide
playwright-cli highlight --hide  # 全部消す
```

> スクリーンショットに合わせてどの要素を指しているか視覚的に示せます。

---

## Chapter 9：トレースとビデオを記録する（20分）

### 9-1. トレース記録（詳細な操作ログ）

```bash
# 記録開始
playwright-cli tracing-start

# 何か操作する
playwright-cli goto https://demo.playwright.dev/todomvc
playwright-cli type "テスト記録"
playwright-cli press Enter
playwright-cli screenshot --filename=after-add.png

# 記録終了（traceファイルが生成される）
playwright-cli tracing-stop
```

生成された `trace.zip` を開いて内容を確認：

```bash
npx playwright show-trace trace.zip
```

> タイムライン・スクリーンショット・ネットワークログが一括で確認できます。

### 9-2. ビデオ記録

```bash
# 記録開始
playwright-cli video-start my-recording.webm

# チャプターマーカーを追加（あとで見やすくなる）
playwright-cli video-chapter "ログイン操作"

# 操作する
playwright-cli type "テスト"
playwright-cli press Enter

playwright-cli video-chapter "タスク完了"
playwright-cli check e21

# 記録終了
playwright-cli video-stop
```

> **使いどころ：**
> バグの再現動画を自動で撮れます。「こういう操作をしたらこうなった」を動画で残せます。

---

## Chapter 10：ストレージとCookieを操作する（20分）

### 10-1. Cookie を操作する

```bash
# Cookie一覧を表示
playwright-cli cookie-list

# 特定ドメインのCookieだけ表示
playwright-cli cookie-list --domain=example.com

# Cookieの値を取得
playwright-cli cookie-get session_id

# Cookieをセットする（テスト用に特定の状態を作りたいとき）
playwright-cli cookie-set test_flag "true"

# Cookieを削除
playwright-cli cookie-delete test_flag

# 全Cookie削除
playwright-cli cookie-clear
```

> **使いどころ：**
> ログイン済み状態のCookieをセットして、ログイン不要でテストを始めるといった使い方ができます。

### 10-2. ローカルストレージを操作する

```bash
playwright-cli localstorage-list
playwright-cli localstorage-get "キー名"
playwright-cli localstorage-set "キー名" "値"
playwright-cli localstorage-delete "キー名"
playwright-cli localstorage-clear
```

### 10-3. セッション状態の保存と復元

```bash
# 現在のCookie・ストレージを丸ごと保存
playwright-cli state-save my-state.json

# 保存した状態を読み込む（ログイン済み状態を復元など）
playwright-cli state-load my-state.json
```

> **使いどころ：**
> 「ログインした状態」を保存しておけば、毎回ログイン操作を繰り返す必要がなくなります。

---

## Chapter 11：ネットワークをモック（差し替え）する（20分）

### 11-1. APIレスポンスを差し替える（route）

「このURLへのリクエストにこの内容を返す」と指定できます。

```bash
# JSONレスポンスをモックする
playwright-cli route "**/api/todos" --body='[{"id":1,"title":"モックデータ","completed":false}]' --content-type=application/json

# リダイレクトさせる
playwright-cli route "**/old-path" --redirect="https://new-path.com"

# アクセスをブロックする（広告・トラッキング除去など）
playwright-cli route "**/ads/**" --block
```

### 11-2. ルート一覧を確認・削除する

```bash
playwright-cli route-list         # 現在設定中のルート一覧
playwright-cli unroute "**/api/todos"  # 特定のルートを削除
playwright-cli unroute            # 全ルートを削除
```

> **使いどころ：**
> - バックエンドAPIがまだできていないのにフロントエンドの表示確認をしたい
> - エラーレスポンスを強制的に返してエラー表示をテストしたい
> - 特定の条件（空データ、大量データなど）を再現したい

---

## Chapter 12：総合演習（40分）

### 課題1：基本操作マスター

以下を順番に実行してください：

```bash
# 1. ヘッドあり（見える状態）でTODOアプリを開く
playwright-cli open https://demo.playwright.dev/todomvc --headed

# 2. タスクを3件追加する（type + press Enter を繰り返す）
playwright-cli type "資料を作る"
playwright-cli press Enter
playwright-cli type "MTGの準備をする"
playwright-cli press Enter
playwright-cli type "議事録を送る"
playwright-cli press Enter

# 3. snapshotでrefを確認する
playwright-cli snapshot

# 4. 1件目を完了にする（refはsnapshotで確認した値を使う）
playwright-cli check <ref番号>

# 5. スクリーンショットを撮る
playwright-cli screenshot --filename=todo-done.png
```

### 課題2：DevTools で調査する

```bash
# 1. サイトを開く
playwright-cli open https://demo.playwright.dev/todomvc --headed

# 2. コンソールログを確認する
playwright-cli console

# 3. ネットワークリクエストを確認する
playwright-cli requests

# 4. 任意の要素のロケーターを生成する
playwright-cli snapshot
playwright-cli generate-locator <ref番号>
```

### 課題3：状態保存と復元

```bash
# 1. TODOアプリを開いてタスクを数件追加する
playwright-cli open https://demo.playwright.dev/todomvc --headed
playwright-cli type "保存テスト1"
playwright-cli press Enter
playwright-cli type "保存テスト2"
playwright-cli press Enter

# 2. ストレージ状態を保存する
playwright-cli state-save todo-state.json

# 3. ブラウザを閉じる
playwright-cli close

# 4. 再度開いて状態を復元する
playwright-cli open https://demo.playwright.dev/todomvc --headed
playwright-cli state-load todo-state.json
playwright-cli reload

# タスクが復元されているか確認！
playwright-cli screenshot --filename=after-restore.png
```

### 課題4（チャレンジ）：ビデオで操作を記録する

```bash
playwright-cli open https://demo.playwright.dev/todomvc --headed
playwright-cli video-start my-workflow.webm
playwright-cli video-chapter "タスク追加"
# ... 自由に操作 ...
playwright-cli video-chapter "タスク完了"
# ... チェックを入れる ...
playwright-cli video-stop
```

録画した動画をチームに共有してみましょう！

---

## Chapter 13：AIエージェントと組み合わせて自然言語で操作する（20分）

### 13-1. playwright-cli は自然言語を直接理解できない

playwright-cli 自体は「決まったコマンド」しか受け付けません。
自然な日本語を入力しても動きません。

```bash
# これは動かない
playwright-cli "牛乳を買うというタスクを追加して"
```

### 13-2. スキルがその橋渡しをしてくれる

Chapter 1 でインストールしたこれ：

```bash
playwright-cli install --skills
```

これは **Claude Code などのAIエージェントに playwright-cli の使い方を教える「説明書」を登録する**作業でした。
この説明書があるからAIが自然言語をコマンドに正しく変換できます。

### 13-3. 全体の仕組み

```
あなた（日本語で指示）
       ↓
Claude Code などのAIエージェント
  ← スキル（説明書）を読んでいる
       ↓
playwright-cli コマンドに自動変換して実行
       ↓
ブラウザが動く
```

### 13-4. 実際に試してみよう

Claude Code を開いて、以下のように日本語で話しかけてみてください：

```
https://demo.playwright.dev/todomvc を開いて、
「資料を作る」「MTGの準備をする」の2件を追加して、
「資料を作る」にチェックを入れてスクリーンショットを撮って
```

するとClaudeが裏で自動的にコマンドを組み立てて実行します：

```bash
# Claudeが自動で実行するコマンド（例）
playwright-cli open https://demo.playwright.dev/todomvc --headed
playwright-cli type "資料を作る"
playwright-cli press Enter
playwright-cli type "MTGの準備をする"
playwright-cli press Enter
playwright-cli snapshot
playwright-cli check e21
playwright-cli screenshot --filename=result.png
```

### 13-5. 自然言語指示のコツ

AIへの指示は**「何を・どこで・どうしたいか」を具体的に**書くほど精度が上がります。

| 曖昧な指示（伝わりにくい） | 具体的な指示（伝わりやすい） |
|--------------------------|--------------------------|
| 「確認して」 | 「ログインして、ダッシュボードのタイトルが表示されているか確認して」 |
| 「テストして」 | 「フォームに空欄のまま送信して、エラーメッセージが出るか確認して」 |
| 「スクショ撮って」 | 「操作後にスクリーンショットを result.png という名前で保存して」 |

### 13-6. どちらの使い方が向いている？

| 状況 | 向いている使い方 |
|------|----------------|
| 手順が決まっていて自分で確認しながら進めたい | コマンドを1行ずつ自分で実行 |
| 複雑な手順をまとめてやってほしい | AIに自然言語で指示 |
| はじめての操作でコマンドがわからない | AIに自然言語で指示し、実行されたコマンドを覚える |
| 繰り返し使う手順を自動化したい | @playwright/test でテストコード化 |

> **まとめ：**
> playwright-cli はコマンドツールですが、AIエージェントと組み合わせることで
> 「日本語で話しかけるだけでブラウザが動く」という使い方ができます。
> スキルのインストールは、この連携を実現するための重要な準備でした。

---

## まとめ：コマンド早見表

### ブラウザ操作
| やりたいこと | コマンド |
|-------------|---------|
| ブラウザを開く（画面表示） | `playwright-cli open <url> --headed` |
| 別のURLへ移動 | `playwright-cli goto <url>` |
| 文字を入力 | `playwright-cli type <text>` |
| キーを押す | `playwright-cli press Enter` |
| クリック | `playwright-cli click <ref>` |
| チェックを入れる | `playwright-cli check <ref>` |
| 要素を調べる | `playwright-cli snapshot` |
| スクリーンショット | `playwright-cli screenshot --filename=xxx.png` |
| PDFで保存 | `playwright-cli pdf --filename=xxx.pdf` |

### セッション管理
| やりたいこと | コマンド |
|-------------|---------|
| セッション一覧 | `playwright-cli list` |
| 別セッションで開く | `playwright-cli -s=名前 open <url>` |
| 状態を保存 | `playwright-cli state-save file.json` |
| 状態を復元 | `playwright-cli state-load file.json` |
| ブラウザを閉じる | `playwright-cli close` |
| 全部閉じる | `playwright-cli close-all` |

### 調査・デバッグ
| やりたいこと | コマンド |
|-------------|---------|
| ダッシュボードを開く | `playwright-cli show` |
| コンソールログ確認 | `playwright-cli console` |
| ネットワーク確認 | `playwright-cli requests` |
| ロケーターを生成 | `playwright-cli generate-locator <ref>` |
| 要素をハイライト | `playwright-cli highlight <ref>` |
| トレース開始 | `playwright-cli tracing-start` |
| トレース終了 | `playwright-cli tracing-stop` |
| ビデオ開始 | `playwright-cli video-start xxx.webm` |
| ビデオ終了 | `playwright-cli video-stop` |

---

## @playwright/test との使い分けまとめ

```
手動で1ステップずつ確認したい
  → playwright-cli

同じ手順を毎回自動で繰り返したい
  → @playwright/test

AIエージェントにブラウザを操作させたい
  → playwright-cli（skillsインストール後）

バグ報告用にビデオ・スクリーンショットを撮りたい
  → playwright-cli

リグレッションテストとして残したい
  → @playwright/test（codegenで録画してコードに変換）
```

---

## 次のステップ

- **コマンド一覧の確認**：`playwright-cli --help`
- **個別コマンドのヘルプ**：`playwright-cli open --help`
- **公式リポジトリ**：https://github.com/microsoft/playwright-cli
- **Playwright MCP**（AIエージェント向けの上位ツール）：https://github.com/microsoft/playwright-mcp

---

> 質問・フィードバックは Slack の `#qa-study` チャンネルへ！
