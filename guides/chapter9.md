# 第9章：その他知っておきたいコマンド・Tips

この章では、作業効率を上げる便利なコマンドと、講座全体のまとめを行います。

---

## 9-1. よく使う便利コマンド

### git rm — Gitの管理下でファイルを削除する

`git rm` はファイルをワークツリーから削除し、同時にその削除をステージングエリアに登録します。

#### 実習：不要なファイルを削除する

テスト用のファイルを作成して、`git rm` で削除する流れを体験します。

```bash
cd ~/and-burger
echo "テスト用ファイル" > test.md
git add test.md
git commit -m "テスト用ファイルを追加"
```

```bash
git rm test.md
```

#### 期待される出力

```
rm 'test.md'
```

```bash
git status
```

#### 期待される出力

```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    test.md
```

```bash
git commit -m "テスト用ファイルを削除"
```

> **補足：** ファイルを手動で削除（`rm test.md`）した場合、削除がステージングされないため、別途 `git add test.md` が必要です。`git rm` はこの2つの操作をまとめて行います。

> **参考：** ディレクトリごと削除する場合は `git rm -r ディレクトリ名` を使います。

---

### git commit -am — ステージングとコミットを同時に行う

変更済みの追跡ファイルに対して、`git add` と `git commit` を1コマンドで実行できます。

#### 実習：-am オプションを体験する

burgers.md を開き、「季節のおすすめ」セクションの説明を変更してください。

変更前：

```markdown
季節ごとに限定メニューをご用意しています。現在のおすすめは店内のボードをご覧ください。
```

変更後：

```markdown
季節ごとに限定メニューをご用意しています。現在のおすすめはスタッフまでお尋ねください。
```

```bash
git commit -am "季節のおすすめの説明文を修正"
```

#### 期待される出力

```
[main xxxxxxx] 季節のおすすめの説明文を修正
 1 file changed, 1 insertion(+), 1 deletion(-)
```

> **補足：** `-am` は `-a`（追跡済みファイルの変更を自動でステージング）と `-m`（メッセージ指定）の組み合わせです。新規ファイルには効かないため、新しいファイルを追加するときは `git add` が必要です。

---

## 9-2. .gitignore の活用

### .gitignore とは

`.gitignore` は、Gitの管理対象外にしたいファイルやディレクトリを指定するための設定ファイルです。ログファイル、一時ファイル、環境設定ファイルなど、リポジトリに含めたくないものを指定します。

### 実習：.gitignore を作成する

#### 手順1：管理対象外にしたいファイルを作成する

```bash
echo "デバッグ用ログ" > debug.log
echo "個人設定" > .env
mkdir tmp
echo "一時ファイル" > tmp/cache.txt
```

```bash
git status
```

#### 期待される出力

```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .env
        debug.log
        tmp/

nothing added to commit but untracked files present (use "git add" to track)
```

#### 手順2：.gitignore を作成する

Code Serverで `.gitignore` を新規作成し、以下の内容を入力してください。

```
# ログファイル
*.log

# 環境設定ファイル
.env

# 一時ファイル
tmp/
```

#### 手順3：効果を確認する

```bash
git status
```

#### 期待される出力

```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

nothing added to commit but untracked files present (use "git add" to track)
```

> **補足：** `.env`、`debug.log`、`tmp/` が一覧から消え、代わりに `.gitignore` だけが表示されます。`.gitignore` に記載したパターンに一致するファイルはGitの追跡対象から除外されます。

#### 手順4：.gitignore をコミットする

`.gitignore` 自体はリポジトリに含めて、チームで共有します。

```bash
git add .gitignore
git commit -m ".gitignoreを追加"
```

### .gitignore のよく使うパターン

| パターン | 意味 |
|---|---|
| `*.log` | 拡張子が .log のすべてのファイル |
| `.env` | .env という名前のファイル |
| `tmp/` | tmp ディレクトリとその中身すべて |
| `build/` | build ディレクトリとその中身すべて |
| `!important.log` | *.log で除外されていても important.log は追跡する |

---

### 参考：既に追跡中のファイルを管理対象外にする — git rm --cached

`.gitignore` に追記しても、すでにGitが追跡しているファイルは除外されません。追跡を解除するには `git rm --cached` を使います。

```bash
git rm --cached ファイル名
```

> **補足：** `--cached` オプションを付けると、ワークツリーからはファイルを削除せず、Gitの追跡だけを解除します。`git rm`（`--cached` なし）はファイル自体も削除するので、注意してください。

---

## 9-3. まとめと Next Step

### 講座全体の復習 — Gitの基本フロー

この講座で学んだGitの操作を、全体の流れとして整理します。

```
[初期設定]
  git config --global user.name / user.email

[ローカルでの作業]
  git init              リポジトリを作成
    ↓
  ファイルを編集
    ↓
  git add               ステージングエリアに登録
    ↓
  git commit            コミット（履歴に記録）
    ↓
  (必要に応じてやり直し)
    git restore         ワークツリーの変更を取り消し
    git commit --amend  直前のコミットを修正
    git reset            コミットを取り消し

[リモートとの連携]
  git remote add        リモートリポジトリを登録
    ↓
  git push              ローカルの変更をリモートに反映
    ↓
  git fetch + merge     リモートの変更をローカルに取り込み
    ↓
  (プッシュ後のやり直し)
    git revert          プッシュ済みの変更を安全に打ち消し

[ブランチ運用]
  git switch -c         ブランチを作成・切り替え
    ↓
  作業 → add → commit
    ↓
  git switch main       mainに戻る
    ↓
  git merge             ブランチをマージ
    ↓
  git branch -d         ブランチを削除
```

### Next Step — 次に学ぶとよいトピック

この講座ではGitの基礎を一通り学びました。次のステップとして、以下のトピックを学ぶことをおすすめします。

| トピック | 内容 |
|---|---|
| Git Flowの残りのブランチ | develop、release、hotfixブランチの運用方法 |
| プルリクエスト（マージリクエスト） | コードレビューを経てからマージする仕組み。チーム開発では必須のワークフロー |
| リベース | コミット履歴を整理するための操作。マージとは異なるブランチ統合の方法 |
| タグ | リリースバージョンなど、特定のコミットに目印を付ける機能 |

---

## 講座で学んだコマンド一覧

### 第1章：Gitを使う準備

| コマンド | 用途 |
|---|---|
| `git --version` | Gitのバージョン確認 |
| `git config --global user.name "名前"` | ユーザー名の設定 |
| `git config --global user.email "メール"` | メールアドレスの設定 |
| `git config --list` | 設定値の一覧表示 |

### 第2章：ローカルリポジトリでの基本操作

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
| `git log --oneline` | コミット履歴の省略表示 |
| `git restore ファイル名` | ワークツリーの変更を取り消し |
| `git restore --staged ファイル名` | ステージングエリアへの登録を取り消し |

### 第3章：リモートリポジトリとの連携

| コマンド | 用途 |
|---|---|
| `git remote add origin URL` | リモートリポジトリを登録 |
| `git remote -v` | 登録済みリモートリポジトリの確認 |
| `git push -u origin main` | 初回プッシュ（上流ブランチの設定を含む） |
| `git push` | リモートリポジトリへのプッシュ |

### 第4章：ブランチの基本

| コマンド | 用途 |
|---|---|
| `git branch` | ブランチの一覧表示 |
| `git switch -c ブランチ名` | ブランチの作成と切り替え |
| `git switch ブランチ名` | ブランチの切り替え |
| `git merge ブランチ名` | 現在のブランチに指定ブランチをマージ |
| `git branch -d ブランチ名` | マージ済みブランチの削除 |

### 第5章：コミット後のやり直し（プッシュ前）

| コマンド | 用途 |
|---|---|
| `git commit --amend -m "メッセージ"` | 直前のコミットメッセージを修正 |
| `git commit --amend --no-edit` | 直前のコミットにファイルを追加 |
| `git reset --soft HEAD~1` | コミットを取り消し（変更はステージングに残る） |
| `git reset --mixed HEAD~1` | コミットとステージングを取り消し（変更はワークツリーに残る） |
| `git reset --hard HEAD~1` | コミット・ステージング・ワークツリーすべてを巻き戻し |

### 第6章：プッシュ後のやり直し

| コマンド | 用途 |
|---|---|
| `git revert HEAD` | 直前のコミットを打ち消す新しいコミットを作成 |
| `git revert コミットハッシュ` | 指定したコミットを打ち消す新しいコミットを作成 |

### 第7章：コンフリクトの理解と対応

| コマンド | 用途 |
|---|---|
| `git merge --abort` | マージを中止してコンフリクト発生前に戻る |

### 第8章：リモートリポジトリからの取得

| コマンド | 用途 |
|---|---|
| `git clone URL [ディレクトリ名]` | リモートリポジトリをローカルにコピー |
| `git fetch` | リモートの変更を取得（マージはしない） |
| `git merge origin/main` | fetchした内容を手動でマージ |
| `git pull` | リモートの変更を取得してマージ（fetch + merge） |

### 第9章：その他知っておきたいコマンド

| コマンド | 用途 |
|---|---|
| `git rm ファイル名` | ファイルの削除とステージングを同時に行う |
| `git commit -am "メッセージ"` | 追跡済みファイルのステージングとコミットを同時に行う |
| `git rm --cached ファイル名` | Gitの追跡を解除（ファイルは残す） |

---

お疲れさまでした。この講座で学んだ内容を土台に、実際のプロジェクトでGitを活用してください。

---

[← 前の章：第8章 リモートリポジトリからの取得](chapter8.md)
