---
title: "個人的Gitコマンドまとめ"
emoji: "🌱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git"]
published: true
---

# はじめに

最近では、SourceTree や GitHub Desktop など、GUI で Git を操作できるツールが数多く登場し、誰でも簡単にバージョン管理を行えるようになった。
しかし、プロジェクトによって使用するツールが異なる場合も多く、それぞれの使い方を覚えるのは手間がかかる。
そのため、すべての Git 操作を CLI（コマンドライン）で統一して管理しようと考え、よく使うコマンドを整理・まとめた。
また、VS Code で開発する際に Git 管理を効率化できる拡張機能についても紹介する。

# Git コマンド

## 基本操作

### `git init`

**説明:** 新しい Git リポジトリを作成する。
**例:**

```bash
git init
```

→ 現在のフォルダを Git 管理下に置く。

---

### `git clone <URL>`

**説明:** 既存のリポジトリをコピー（複製）する。
**例:**

```bash
git clone https://github.com/user/repo.git
```

→ GitHub 上のリポジトリをローカルにダウンロード。

---

### `git status`

**説明:** 現在の変更状態（未コミットファイルなど）を確認。
**例:**

```bash
git status
```

→ 変更済み・未追跡ファイルの一覧を表示。

---

### `git add <file>`

**説明:** コミットするファイルをステージに追加。
**例:**

```bash
git add index.html
```

→ `index.html` をコミット対象に追加。

---

### `git add .`

**説明:** すべての変更をステージング。
**例:**

```bash
git add .
```

→ カレントディレクトリ以下の変更をすべて追加。

---

### `git commit -m "メッセージ"`

**説明:** ステージングした変更をコミットとして記録。
**例:**

```bash
git commit -m "Fix login bug"
```

→ 「Fix login bug」というメッセージでコミット。

---

### `git log`

**説明:** コミット履歴を表示。
**例:**

```bash
git log --oneline
```

→ 簡潔な履歴を 1 行ずつで確認。

---

### `git diff`

**説明:** ファイルの変更内容の差分を表示。
**例:**

```bash
git diff
```

→ ステージングされていない差分を確認。

---

## ブランチ操作

### `git branch`

**説明:** ブランチの一覧を表示。
**例:**

```bash
git branch
```

→ 現在のブランチと全ブランチ一覧を表示。

---

### `git branch <name>`

**説明:** 新しいブランチを作成。
**例:**

```bash
git branch feature/add-login
```

→ `feature/add-login` ブランチを作成。

---

### `git checkout <name>`

**説明:** 指定したブランチに切り替え。
**例:**

```bash
git checkout feature/add-login
```

---

### `git checkout -b <name>`

**説明:** ブランチを作成して即座に切り替え。
**例:**

```bash
git checkout -b feature/add-login
```

---

### `git switch <name>`

**説明:** ブランチを切り替える新しいコマンド（2.23 以降）。
**例:**

```bash
git switch develop
```

---

### `git merge <branch>`

**説明:** 他のブランチを現在のブランチに統合。
**例:**

```bash
git merge feature/add-login
```

---

### `git branch -d <name>`

**説明:** 不要になったブランチを削除。
**例:**

```bash
git branch -d feature/add-login
```

---

## リモート操作

### `git remote -v`

**説明:** 設定されているリモートリポジトリを確認。
**例:**

```bash
git remote -v
```

---

### `git remote add origin <URL>`

**説明:** リモートリポジトリを登録。
**例:**

```bash
git remote add origin https://github.com/user/repo.git
```

---

### `git fetch`

**説明:** リモートの最新情報を取得（マージなし）。
**例:**

```bash
git fetch origin
```

---

### `git pull`

**説明:** リモートの変更を取得して自動マージ。
**例:**

```bash
git pull origin main
```

---

### `git push origin <branch>`

**説明:** ローカルの変更をリモートに送信。
**例:**

```bash
git push origin feature/add-login
```

---

## 修正・復旧系

### `git restore <file>`

**説明:** 変更を取り消して直前の状態に戻す。
**例:**

```bash
git restore index.html
```

---

### `git reset HEAD <file>`

**説明:** ステージングを解除する。
**例:**

```bash
git reset HEAD index.html
```

---

### `git reset --hard <commit>`

**説明:** 指定コミット時点に完全に戻す（注意 ⚠️）。
**例:**

```bash
git reset --hard abc1234
```

---

### `git stash`

**説明:** 作業中の変更を一時退避。
**例:**

```bash
git stash
```

---

### `git stash pop`

**説明:** 一時退避した変更を戻す。
**例:**

```bash
git stash pop
```

---

### `git rebase <branch>`

**説明:** 別ブランチ上にコミットを付け直す（履歴を整理）。
**例:**

```bash
git rebase main
```

---

### `git cherry-pick <commit>`

**説明:** 特定のコミットを別ブランチへ適用。
**例:**

```bash
git cherry-pick abc1234
```

完璧です！
以下に、チーム開発でよく使われる２つのワークフローをそれぞれ解説します 👇

## 一連の実践フロー

### **① rebase パターン（履歴をきれいに保つ）**

- 特徴：履歴が直線的で見やすい。
- 注意点：コンフリクト時の扱いに少し慣れが必要。

```bash
# ① リポジトリをクローン
git clone https://github.com/user/repo.git

# ② プロジェクトフォルダに移動
cd repo

# ③ 新しい作業ブランチを作成して切り替え
git switch -c feature/add-login

# ④ コードを編集（新機能などを実装）

# ⑤ 変更をステージングしてコミット
git add .
git commit -m "Add login feature"

# ⑥ mainブランチへ切り替え、最新を取得
git switch main
git pull origin main

# ⑦ 作業ブランチに戻る
git switch feature/add-login

# ⑧ 最新のmainブランチの変更をrebaseで取り込む
#    → 自分のコミットを最新mainの上に載せ替える
git rebase main

# （コンフリクトが出た場合）
#    1. 該当ファイルを修正
#    2. 修正後に以下を実行
git add .
git rebase --continue

# ⑨ コンフリクトがなければpush
git push origin feature/add-login

# ※ 初回pushでエラーが出たら強制pushが必要になることも
git push -f origin feature/add-login  # rebase後は履歴が変わるため
```

#### rebase パターンのポイント

| メリット                        | デメリット                               |
| ------------------------------- | ---------------------------------------- |
| 履歴が一本化され、読みやすい    | 強制 push（`-f`）が必要なことがある      |
| main ブランチがすっきり保たれる | コンフリクト対応に手間がかかる           |
| チームレビュー時に差分が明確    | 複数人で同ブランチを触ると混乱することも |

---

### **② merge パターン（安全でシンプル）**

- 特徴：履歴がそのまま残る。安全でわかりやすい。
- 注意点：履歴に「Merge commit」が増える。

```bash
# ① リポジトリをクローン
git clone https://github.com/user/repo.git

# ② プロジェクトフォルダに移動
cd repo

# ③ 新しい作業ブランチを作成して切り替え
git switch -c feature/add-login

# ④ コードを編集（新機能などを実装）

# ⑤ 変更をステージングしてコミット
git add .
git commit -m "Add login feature"

# ⑥ mainブランチへ切り替え、最新を取得
git switch main
git pull origin main

# ⑦ 作業ブランチに戻る
git switch feature/add-login

# ⑧ 最新mainの変更をmergeで取り込む
#    → 変更点を統合しつつ履歴も残る
git merge main

# （コンフリクトが出た場合）
#    1. 該当ファイルを修正
#    2. 修正後に以下を実行
git add .
git commit  # merge完了のコミットを作成

# ⑨ リモートへpush
git push origin feature/add-login
```

#### merge パターンのポイント

| メリット                                | デメリット                               |
| --------------------------------------- | ---------------------------------------- |
| 履歴が安全に残る（rebase よりリスク少） | 「Merge commit」が増えて履歴が複雑になる |
| チームで同時に開発しても衝突しにくい    | 履歴を追うのが少し大変になる             |
| 強制 push 不要（`git push`で OK）       | main 履歴が枝分かれしやすい              |

---

### rebase vs merge

| 項目                 | rebase                           | merge                    |
| -------------------- | -------------------------------- | ------------------------ |
| 履歴の見やすさ       | ◎ 直線的                         | △ 分岐が多い             |
| コンフリクト対応     | △ 難易度高め                     | ○ 比較的簡単             |
| 強制 push の必要     | あり（-f）                       | なし                     |
| チーム開発での安全性 | △                                | ◎                        |
| おすすめ場面         | 個人開発や綺麗な履歴を保ちたい時 | チーム開発や安全重視の時 |

# Git 管理拡張機能

### 1. GitLens（ギットレンズ）

#### 概要

GitLens は「誰が・いつ・なぜ」コードを変更したのかを可視化する強力な拡張機能である。
Git の履歴情報をエディタ内でリッチに表示できる点が特徴である。

#### 主な機能

- **Blame アノテーション表示**
  各行の右側に「この行を最後に編集した人」と「日時」を表示する。
  → 「このコードを誰が書いたのか」をすぐに把握できる。
- **コミット履歴の詳細表示**
  コミットメッセージ、変更ファイル、差分（diff）をポップアップで確認できる。
- **ファイルやリポジトリ単位の履歴表示**
  ファイル全体の変更履歴を時系列で閲覧できる。
- **リポジトリ内のブランチ比較や検索**
  どのブランチで何が変更されたのかを視覚的に確認できる。

#### 利用シーン

- チーム開発で「誰がいつ修正したのか」を即座に把握したい場合。
- 過去の修正意図をコードからたどりたい場合。

---

### 2. Git Graph（ギットグラフ）

#### 概要

Git Graph は、ブランチ構造やコミットの流れを「グラフ」で可視化する拡張機能である。

#### 主な機能

- **ブランチ構成の可視化**
  master / develop / feature ブランチなどの分岐や統合を一目で確認できる。
- **ドラッグ＆ドロップ操作**
  GUI 上でブランチ切り替え・マージ・リベースを簡単に行うことができる。
- **コミット差分やメッセージの確認**
  各コミットをクリックして詳細情報を表示できる。

#### 利用シーン

- ブランチが増えて複雑化したプロジェクトの全体像を把握したい場合。
- 視覚的にマージの流れを確認したい場合。

---

### 3. Git History（ギットヒストリー）

#### 概要

Git History は、特定のファイルやリポジトリ全体の「コミット履歴」を時系列で確認できる拡張機能である。

#### 主な機能

- **リポジトリ全体の履歴閲覧**
  いつ、どのような変更が行われたのかをリスト形式で表示する。
- **特定ファイルの変更履歴追跡**
  ファイルを右クリックし、「Git: View File History」で変更履歴を確認できる。
- **差分（diff）の比較**
  コミット間の差分を確認することができる。

#### 利用シーン

- 過去のコミットを特定して確認したい場合。
- ファイル単位で「いつ・どのように」変更されたのかを追跡したい場合。
