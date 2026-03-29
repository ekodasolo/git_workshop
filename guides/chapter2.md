# 第2章：ローカルリポジトリでの基本操作

この章では、Gitの基本サイクル（編集 → ステージング → コミット）を体験します。講座の核心部分です。

---

## 2-1. ローカルリポジトリの新規作成

### Gitの3つの領域

Gitには、ファイルが管理される3つの領域があります。この3層構造の理解が、Gitを使いこなす最大のポイントです。

| 領域 | 説明 |
|---|---|
| ワークツリー | 実際にファイルを編集する場所（作業ディレクトリそのもの） |
| ステージングエリア | 次のコミットに含める変更を登録する場所 |
| Gitディレクトリ（リポジトリ） | コミット履歴が保存される場所（`.git` フォルダ） |

ファイルの変更は「ワークツリー → ステージングエリア → Gitディレクトリ」の順に進みます。

### 手順：リポジトリの作成

作業用ディレクトリを作成し、`git init` でリポジトリを初期化します。

```bash
mkdir and-burger
cd and-burger
git init
```

### 期待される出力

```
Initialized empty Git repository in /home/user/and-burger/.git/
```

> **補足：** `git init` を実行すると、ディレクトリ内に `.git` フォルダが作成されます。このフォルダがGitディレクトリ（リポジトリ）の実体です。

---

## 2-2. Gitの基本フロー — add と commit

### 実習1：README.md を作成して最初のコミット

#### 手順1：README.md を作成する

Code Serverで `README.md` を新規作成し、以下の内容を入力してください。

```markdown
# AND BURGER

こだわりの食材を使ったグルメバーガー専門店。一つひとつ丁寧に焼き上げたパティと、厳選したトッピングの組み合わせをお楽しみください。
```

#### 手順2：ステージングエリアに登録する

```bash
git add README.md
```

> **補足：** `git add` は「この変更を次のコミットに含める」という意味です。ファイルをワークツリーからステージングエリアに登録します。

#### 手順3：コミットする

```bash
git commit -m "最初のコミット：README.mdを作成"
```

### 期待される出力

```
[main (root-commit) xxxxxxx] 最初のコミット：README.mdを作成
 1 file changed, 3 insertions(+)
 create mode 100644 README.md
```

> **補足：** `-m` オプションでコミットメッセージを指定します。メッセージには「何をしたか」を簡潔に書きます。

---

### 実習2：burgers.md を作成して2回目のコミット

#### 手順1：burgers.md を作成する

Code Serverで `burgers.md` を新規作成し、以下の内容を入力してください。

```markdown
# バーガーメニュー

AND BURGERこだわりのグルメバーガーをご紹介します。

---

## クラシックバーガー

粗挽きビーフ100%パティにフレッシュトマト、レタス、オリジナルソースを合わせた定番の一品。

**¥1,200**

---

## アボカドチーズバーガー

たっぷりのアボカドスライスと濃厚チェダーチーズをトッピング。クリーミーな味わいが人気です。

**¥1,400**

---

## テリヤキバーガー

特製テリヤキソースで仕上げたジューシーなパティに、シャキシャキのレタスとマヨネーズを添えて。

**¥1,300**

---

## スパイシーチリバーガー

自家製チリソースとハラペーニョがアクセント。ピリッとした刺激がクセになるバーガーです。

**¥1,400**

---

[← トップに戻る](README.md)
```

#### 手順2：README.md にバーガーメニューへのリンクを追加する

README.md を開き、末尾に以下を追記してください。

```markdown

---

## メニュー

- [バーガーメニュー](burgers.md) — 自慢のグルメバーガーをご紹介
```

#### 手順3：両方のファイルをまとめてステージングする

```bash
git add -A
```

> **補足：** `git add -A` はワークツリー内のすべての変更（新規ファイル・変更・削除）をまとめてステージングエリアに登録します。

#### 手順4：コミットする

```bash
git commit -m "バーガーメニューを追加"
```

### 期待される出力

```
[main xxxxxxx] バーガーメニューを追加
 2 files changed, 44 insertions(+)
 create mode 100644 burgers.md
```

---

## 2-3. 状態確認のコマンド — status, diff, log

ここでは「今どうなっているか確認する」習慣を身につけます。**困ったらまず `git status`** と覚えてください。

### 実習：burgers.md を編集して各コマンドを体験する

#### 手順1：burgers.md を編集する

burgers.md を開き、クラシックバーガーの価格を変更してください。

変更前：

```markdown
**¥1,200**
```

変更後：

```markdown
**¥1,250**
```

#### 手順2：git status で状態を確認する

```bash
git status
```

### 期待される出力

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   burgers.md

no changes added to commit (use "git add" to track)
```

> **補足：** `modified: burgers.md` は「ワークツリーで変更されたが、まだステージングエリアには登録されていない」状態を示しています。

#### 手順3：git diff でワークツリーの変更内容を確認する

```bash
git diff
```

### 期待される出力

```diff
diff --git a/burgers.md b/burgers.md
--- a/burgers.md
+++ b/burgers.md
@@ -xx,x +xx,x @@
-**¥1,200**
+**¥1,250**
```

> **補足：** `-` が変更前、`+` が変更後の行です。ワークツリーとステージングエリアの差分が表示されます。

#### 手順4：ステージングして git diff --cached を確認する

```bash
git add burgers.md
git diff --cached
```

### 期待される出力

```diff
diff --git a/burgers.md b/burgers.md
--- a/burgers.md
+++ b/burgers.md
@@ -xx,x +xx,x @@
-**¥1,200**
+**¥1,250**
```

> **補足：** `git diff --cached` は、ステージングエリアとGitディレクトリ（直前のコミット）の差分を表示します。`git diff` がワークツリー ↔ ステージングエリアの差分だったのに対し、`git diff --cached` はステージングエリア ↔ Gitディレクトリの差分です。この違いが3層構造の理解につながります。

#### 手順5：コミットして git log で履歴を確認する

```bash
git commit -m "クラシックバーガーの価格を更新"
git log
```

### 期待される出力

```
commit xxxxxxx (HEAD -> main)
Author: 自分の名前 <自分のメールアドレス>
Date:   ...

    クラシックバーガーの価格を更新

commit xxxxxxx
Author: 自分の名前 <自分のメールアドレス>
Date:   ...

    バーガーメニューを追加

commit xxxxxxx
Author: 自分の名前 <自分のメールアドレス>
Date:   ...

    最初のコミット：README.mdを作成
```

> **補足：** `git log` はコミット履歴を新しい順に表示します。各コミットにはハッシュ値（xxxxxxx）、作者、日時、メッセージが記録されています。

#### 手順6：差分付きで履歴を確認する

```bash
git log -p
```

> **補足：** `git log -p` は各コミットの変更内容（diff）もあわせて表示します。「いつ・何を変更したか」が一目でわかります。表示を終了するには `q` キーを押してください。

---

## 2-4. コミット前のやり直し

「間違えても戻せる」という安心感を持つことが、この節の目的です。

### 実習1：ワークツリーの変更を取り消す — git restore

#### 手順1：burgers.md を編集する

burgers.md を開き、テリヤキバーガーの説明を適当に書き換えてください（何でも構いません）。

#### 手順2：変更を確認する

```bash
git status
git diff
```

変更されていることが確認できます。

#### 手順3：変更を取り消す

```bash
git restore burgers.md
```

#### 手順4：取り消されたことを確認する

```bash
git status
git diff
```

### 期待される出力

```
On branch main
nothing to commit, working tree clean
```

> **補足：** `git restore` はワークツリーの変更を直前のコミット（正確にはステージングエリア）の状態に戻します。まだコミットしていない変更を「やっぱりやめたい」ときに使います。

---

### 実習2：ステージングを取り消す — git restore --staged

#### 手順1：burgers.md を編集してステージングする

burgers.md を開き、スパイシーチリバーガーの価格を `¥1,500` に変更してください。

```bash
git add burgers.md
```

#### 手順2：ステージングされていることを確認する

```bash
git status
```

### 期待される出力

```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   burgers.md
```

#### 手順3：ステージングを取り消す

```bash
git restore --staged burgers.md
```

#### 手順4：状態を確認する

```bash
git status
```

### 期待される出力

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   burgers.md

no changes added to commit (use "git add" to track)
```

> **補足：** `git restore --staged` はステージングエリアからの登録を取り消します。ファイルの変更内容自体はワークツリーに残ったままです。「`git add` したけどやっぱりまだコミットしたくない」ときに使います。

#### 手順5：ワークツリーの変更も取り消して元に戻す

```bash
git restore burgers.md
```

---

## この章のまとめ

この章で学んだコマンドは以下の通りです。

| コマンド | 用途 |
|---|---|
| `git init` | ローカルリポジトリの作成 |
| `git add ファイル名` | 指定ファイルをステージングエリアへ登録 |
| `git add -A` | 全変更をまとめてステージングエリアへ登録 |
| `git commit -m "メッセージ"` | コミットメッセージを指定してコミット |
| `git status` | ローカルリポジトリの状態確認 |
| `git diff` | ワークツリーとステージングエリアの差分表示 |
| `git diff --cached` | ステージングエリアとGitディレクトリの差分表示 |
| `git log` | コミット履歴の表示 |
| `git log -p` | 差分付きコミット履歴の表示 |
| `git restore ファイル名` | ワークツリーの変更を取り消し |
| `git restore --staged ファイル名` | ステージングエリアへの登録を取り消し |

---

[← 前の章：第1章 Gitを使う準備](chapter1.md) | [次の章へ：第3章 リモートリポジトリとの連携 →](chapter3.md)
