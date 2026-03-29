# 第8章：リモートリポジトリからの取得

この章では、リモートリポジトリからローカルにコードを取得する方法を学びます。第3章ではローカルからリモートへの「プッシュ」を扱いましたが、ここではその逆方向の操作です。

---

## 8-1. git clone — 既存リポジトリのコピー

### clone とは

`git clone` は、リモートリポジトリの内容をまるごとローカルにコピーするコマンドです。新しくプロジェクトに参加するときの最初のステップとして使います。

`git init` が「空のリポジトリを作る」のに対し、`git clone` は「既存のリポジトリをコピーする」という違いがあります。

---

### 実習：CodeCommit上のリポジトリをcloneする

#### 手順1：別のディレクトリに移動する

現在の `and-burger` ディレクトリの外に移動します。

```bash
cd ~
```

#### 手順2：リポジトリをcloneする

```bash
git clone https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger and-burger-clone
```

> **補足：** URLの後に指定した `and-burger-clone` はクローン先のディレクトリ名です。省略するとリポジトリ名（`and-burger`）がそのまま使われます。今回は元のディレクトリと区別するために別の名前を付けています。

### 期待される出力

```
Cloning into 'and-burger-clone'...
remote: Counting objects: xx, done.
Receiving objects: 100% (xx/xx), done.
Resolving deltas: 100% (xx/xx), done.
```

#### 手順3：cloneしたリポジトリの内容を確認する

```bash
cd and-burger-clone
ls
```

### 期待される出力

```
README.md  burgers.md  drinks.md  info.md  sides.md
```

#### 手順4：コミット履歴を確認する

```bash
git log --oneline
```

リモートリポジトリと同じコミット履歴がすべて取得できていることを確認してください。

#### 手順5：リモートリポジトリの設定を確認する

```bash
git remote -v
```

### 期待される出力

```
origin  https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger (fetch)
origin  https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger (push)
```

> **補足：** `git clone` を実行すると、クローン元のリモートリポジトリが自動的に `origin` として登録されます。第3章で手動で `git remote add` した設定が、cloneでは自動で行われます。

---

## 8-2. git pull と git fetch

### pull と fetch の違い

チーム開発では、他のメンバーがリモートリポジトリにプッシュした変更を自分のローカルに取り込む必要があります。この操作には2つの方法があります。

| コマンド | 動作 |
|---|---|
| `git fetch` | リモートの変更を取得するだけ（マージはしない） |
| `git pull` | リモートの変更を取得し、自動的にマージする（`git fetch` + `git merge`） |

**本講座では `git fetch` + `git merge` の2段階操作を推奨します。** `git pull` は手軽ですが、リモートの変更内容を確認せずに自動でマージされるため、意図しない変更が取り込まれたり、予期しないコンフリクトが発生したりする可能性があります。`git fetch` で取得してから内容を確認し、納得したうえで `git merge` する方が安全です。

---

### 実習：pull と fetch を体験する

2つのディレクトリ（`and-burger` と `and-burger-clone`）を使って、一方で変更をプッシュし、もう一方で取得する流れを体験します。

#### 手順1：元のリポジトリで変更をプッシュする

元の `and-burger` ディレクトリに戻ります。

```bash
cd ~/and-burger
```

burgers.md を開き、末尾の `[← トップに戻る](README.md)` の直前に以下を追加してください。

```markdown

## 季節のおすすめ

季節ごとに限定メニューをご用意しています。詳しくはスタッフまでお声がけください。

---
```

```bash
git add burgers.md
git commit -m "季節のおすすめセクションを追加"
git push
```

---

#### 手順2：clone先のリポジトリで git pull する

```bash
cd ~/and-burger-clone
```

まず、現在のburgers.mdには「季節のおすすめ」がないことを確認してください。

```bash
git pull
```

### 期待される出力

```
remote: Counting objects: x, done.
Unpacking objects: 100% (x/x), done.
From https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger
   xxxxxxx..xxxxxxx  main       -> origin/main
Updating xxxxxxx..xxxxxxx
Fast-forward
 burgers.md | 6 ++++++
 1 file changed, 6 insertions(+)
```

burgers.md を開いて「季節のおすすめ」セクションが追加されていることを確認してください。

> **補足：** `git pull` は、リモートの変更を取得（fetch）してから自動的にマージ（merge）まで行います。

---

#### 手順3：元のリポジトリでさらに変更をプッシュする

```bash
cd ~/and-burger
```

burgers.md の「季節のおすすめ」セクションの説明を変更してください。

変更前：

```markdown
季節ごとに限定メニューをご用意しています。詳しくはスタッフまでお声がけください。
```

変更後：

```markdown
季節ごとに限定メニューをご用意しています。現在のおすすめは店内のボードをご覧ください。
```

```bash
git add burgers.md
git commit -m "季節のおすすめの説明文を更新"
git push
```

---

#### 手順4：clone先のリポジトリで git fetch する

```bash
cd ~/and-burger-clone
git fetch
```

### 期待される出力

```
remote: Counting objects: x, done.
Unpacking objects: 100% (x/x), done.
From https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger
   xxxxxxx..xxxxxxx  main       -> origin/main
```

#### 手順5：fetchした内容を確認する

```bash
git log --oneline origin/main
```

リモートの最新コミット「季節のおすすめの説明文を更新」が表示されます。

```bash
git log --oneline main
```

ローカルのmainにはまだこのコミットが反映されていないことを確認してください。

> **補足：** `git fetch` はリモートの情報を取得するだけで、ローカルのブランチは変更しません。`origin/main`（リモート追跡ブランチ）は更新されますが、ローカルの `main` はそのままです。

#### 手順6：手動でマージする

```bash
git merge origin/main
```

### 期待される出力

```
Updating xxxxxxx..xxxxxxx
Fast-forward
 burgers.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

burgers.md を開いて、説明文が更新されていることを確認してください。

> **補足：** `git fetch` + `git merge` の2段階で行うことで、「どんな変更が来るか確認してからマージする」という慎重な運用ができます。

---

#### 手順7：元のディレクトリに戻る

以降の章は元のディレクトリで作業します。

```bash
cd ~/and-burger
```

---

## この章のまとめ

この章で学んだコマンドは以下の通りです。

| コマンド | 用途 |
|---|---|
| `git clone URL [ディレクトリ名]` | リモートリポジトリをローカルにコピー |
| `git pull` | リモートの変更を取得してマージ |
| `git fetch` | リモートの変更を取得のみ（マージはしない） |
| `git merge origin/main` | fetchした内容を手動でマージ |
| `git log --oneline origin/main` | リモート追跡ブランチの履歴を確認 |

### リモートの変更を取り込むときの推奨フロー

```
1. git fetch                       ← リモートの変更を取得
2. git log --oneline origin/main   ← どんな変更があるか確認
3. git merge origin/main           ← 納得したらマージ
```

`git pull` は上記の手順1と3をまとめて実行するショートカットです。一人で作業しているときなど、リモートの変更内容が自明な場合には便利ですが、チーム開発では `git fetch` + `git merge` の方が安全です。

---

[← 前の章：第7章 コンフリクトの理解と対応](chapter7.md) | [次の章へ：第9章 その他知っておきたいコマンド →](chapter9.md)
