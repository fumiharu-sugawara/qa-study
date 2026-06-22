# Git 基本コマンド ハンズオン

---

## 今日のコマンド一覧

```
git add         → 変更を「次のセーブ候補」に入れる
git commit      → セーブポイントとして記録する
git branch      → 今あるブランチの一覧を見る
git checkout -b → 新しいブランチを作って移動する
git checkout    → 別のブランチに移動する
git push        → 記録をGitHubにアップロードする
```

---

## Part 1 : `git add` — 付箋を貼る

### たとえ話

お客様に提出するレポートを書いているとします。
机の上にいくつか変更したページが散らばっています。

`git add` は「このページを次の提出物に含める」と **付箋を貼る** 作業です。
付箋を貼っただけで、まだ封筒には入っていません。

### 基本の使い方

```bash
# 特定のファイルだけ候補に入れる
git add ファイル名

# 例
git add README.md

# 変更したファイルをすべて候補に入れる
git add .
```

### 状態を確認する

```bash
git status
```

実行すると…

```
Changes to be committed:    ← addした（付箋あり）
  modified: README.md

Changes not staged:         ← まだaddしていない（付箋なし）
  modified: notes.txt
```

---

## Part 2 : `git commit` — セーブポイントを作る

### たとえ話

付箋を貼ったページを封筒に入れて、**日付とメモを書いて保存する** イメージです。

ゲームで言えば「ここでセーブ」のボタンを押す瞬間。
何かあったらこの時点に戻ってこれます。

### 基本の使い方

```bash
git commit -m "メッセージ"
```

`-m` の後の文字列が **セーブポイントにつける名前**（メモ）です。

```bash
# 例
git commit -m "ログイン画面のボタンの色を変更"
git commit -m "テストケースを3件追加"
```

### コミットの履歴を見る

```bash
git log --oneline
```

```
a3f9c12 トップページのタイトルを日本語に変更
7e1b304 ヘッダーのロゴを更新
2c8d001 初期ファイルを作成
```


---

## Part 3 : `git push` — GitHubにアップロードする

### たとえ話

`git commit` までの記録は **自分のPC（ローカル）の中だけ** にある状態です。
チームメンバーはまだ見られません。

`git push` は、そのセーブデータを **共有ロッカー（GitHub）に入れる** 作業です。
pushして初めて、他の人が変更を見たりレビューしたりできます。

```
自分のPC（ローカル）          GitHub（リモート）
┌─────────────────┐           ┌─────────────────┐
│  git add        │           │                 │
│  git commit     │  ──push──▶│  みんなが見える  │
│  （ここまでは   │           │                 │
│   自分だけ）    │           │                 │
└─────────────────┘           └─────────────────┘
```

### 基本の使い方

```bash
git push origin ブランチ名
```

`origin` は「GitHubのリポジトリ」を指す名前です（デフォルトでそう呼ばれています）。

```bash
# 例：feature/contact-page ブランチをpushする
git push origin feature/contact-page
```

### pushしたらPRを作る

pushするとGitHubに「このブランチをPRにしますか？」というリンクが表示されます。
そこからプルリクエスト（PR）を作成して、レビューを依頼します。

---

## ハンズオン練習

今日学んだコマンドを通しで体験します。
このリポジトリ（qa-study）を使って、実際にブランチを作ってPRを出すところまでやってみましょう。

### 手順

**1. 作業用ブランチを作る**

```bash
git checkout -b feature/自分の名前-practice
# 例：git checkout -b feature/yamada-practice
```

**2. 自分のメモファイルを編集する**

`members/` フォルダの自分のファイルを開いて、一言追記します。

```bash
# 例（VSCodeで開く場合）
code members/自分の名前.md
```

**3. 変更を記録に含める**

```bash
git add members/自分の名前.md
```

ちゃんとaddされたか確認：

```bash
git status
# "Changes to be committed" に自分のファイルが表示されればOK
```

**4. コミットする**

```bash
git commit -m "練習：自分のメモを更新"
```

履歴を確認：

```bash
git log --oneline
# 自分のコミットが一番上に表示される
```

**5. GitHubにpushする**

```bash
git push -u origin feature/自分の名前-practice
```

成功するとこんなメッセージが出ます：

```
To https://github.com/...
 * [new branch]  feature/yamada-practice -> feature/yamada-practice
```

**6. GitHubでPRを作成する**

ブラウザでこのリポジトリを開くと黄色いバナーが表示されます。

```
Compare & pull request  ← このボタンをクリック
```

タイトルと説明を書いて「Create pull request」を押せば完了です。

---
