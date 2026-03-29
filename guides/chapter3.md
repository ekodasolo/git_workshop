# 第3章：リモートリポジトリとの連携（CodeCommit編）

この章では、ローカルリポジトリの内容をリモートリポジトリ（AWS CodeCommit）にプッシュする方法を学びます。ローカルだけで完結していた作業を、チームで共有できるようにするステップです。

---

## 3-1. リモートリポジトリとは

### ローカルリポジトリとリモートリポジトリの関係

ここまでの操作はすべて自分のPC（ローカル）の中で完結していました。しかし実際の開発では、チームメンバーとコードを共有したり、バックアップとしてサーバーに保存したりする必要があります。

| 種類 | 場所 | 役割 |
|---|---|---|
| ローカルリポジトリ | 自分のPC上 | 手元で編集・コミットする作業場所 |
| リモートリポジトリ | サーバー上 | チームでの共有・バックアップの拠点 |

ローカルリポジトリとリモートリポジトリは独立した存在で、明示的に同期（プッシュやプル）を行うことでつながります。

> **補足：** リモートリポジトリの代表的なホスティングサービスには、GitHub、GitLab、Bitbucket、AWS CodeCommitなどがあります。本講座ではAWS CodeCommitを使用します。

---

## 3-2. CodeCommitでリモートリポジトリを作成

### CodeCommitとは

AWS CodeCommitは、AWSが提供するGitベースのリポジトリホスティングサービスです。AWS環境との親和性が高く、IAMによるアクセス制御が可能です。

### 手順1：AWSマネジメントコンソールにログインする

ブラウザでAWSマネジメントコンソールを開き、講師から共有されたIAMユーザーでログインしてください。

### 手順2：CodeCommitのコンソールを開く

サービス検索バーに「CodeCommit」と入力し、CodeCommitのコンソールを開きます。

### 手順3：リポジトリを作成する

1. 「リポジトリを作成」ボタンをクリックします。
2. 以下の情報を入力します。

| 項目 | 入力値 |
|---|---|
| リポジトリ名 | `and-burger` |
| 説明 | （任意）Git基礎講座の実習用リポジトリ |

3. 「作成」ボタンをクリックします。

### 期待される結果

リポジトリが作成され、接続手順のページが表示されます。このページに記載されているHTTPS URLを次の手順で使用します。

> **補足：** URLの形式は `https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger` です。リージョン部分は環境によって異なります。

---

## 3-3. CodeCommitへの接続設定

本講座の実習環境はEC2上のCode Serverです。EC2にはCodeCommitへのアクセス権限を持つIAMロール（インスタンスロール）が設定されているため、IAMユーザーの認証情報を個別に作成する必要はありません。

Gitがインスタンスロールの認証情報を使ってCodeCommitに接続できるよう、credential helperを設定します。

### 手順1：credential helperを設定する

```bash
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```

### 手順2：設定を確認する

```bash
git config --global --get credential.helper
git config --global --get credential.UseHttpPath
```

### 期待される出力

```
!aws codecommit credential-helper $@
true
```

> **補足：** この設定により、`git push` や `git pull` の際にAWS CLIが自動的にEC2のインスタンスロールから一時的な認証情報を取得し、CodeCommitへの認証を行います。ユーザー名やパスワードの入力は不要です。

---

## 3-4. リモートリポジトリの登録とプッシュ

### 手順1：リモートリポジトリを登録する

ターミナルで `and-burger` ディレクトリに移動し、以下のコマンドを実行してください。URLは手順3-2で確認したものに置き換えてください。

```bash
git remote add origin https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger
```

> **補足：** `origin` はリモートリポジトリに付ける名前（エイリアス）です。慣例として最初のリモートリポジトリには `origin` という名前を付けます。

### 手順2：登録を確認する

```bash
git remote -v
```

### 期待される出力

```
origin  https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger (fetch)
origin  https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger (push)
```

> **補足：** `fetch` と `push` の2行が表示されます。`fetch` はリモートから取得する際のURL、`push` はリモートに送信する際のURLです。通常は同じURLが設定されます。

### 手順3：リモートリポジトリにプッシュする

```bash
git push -u origin main
```

### 期待される出力

```
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to X threads
Compressing objects: 100% (9/9), done.
Writing objects: 100% (9/9), xxxx bytes | xxxx bytes/s, done.
Total 9 (delta X), reused 0 (delta 0)
To https://git-codecommit.<リージョン>.amazonaws.com/v1/repos/and-burger
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

> **補足：** `-u`（`--set-upstream`）オプションは、ローカルの `main` ブランチとリモートの `origin/main` ブランチを紐づける設定です。一度設定すれば、以降は `git push` だけでプッシュできるようになります。

### 手順4：CodeCommitのコンソールで確認する

ブラウザでCodeCommitのコンソールに戻り、リポジトリのページを更新してください。

### 期待される結果

ローカルリポジトリの内容（README.md、burgers.md）がCodeCommit上に表示されます。コミット履歴もローカルと同じ内容が確認できます。

---

## この章のまとめ

この章で学んだコマンドは以下の通りです。

| コマンド | 用途 |
|---|---|
| `git remote add origin URL` | リモートリポジトリを登録 |
| `git remote -v` | 登録済みリモートリポジトリの確認 |
| `git push -u origin main` | リモートリポジトリへの初回プッシュ（上流ブランチの設定を含む） |
| `git push` | リモートリポジトリへのプッシュ（上流ブランチ設定済みの場合） |

### ここまでの全体の流れ

```
[第1章] Gitの準備（インストール・初期設定）
    ↓
[第2章] ローカルで基本操作（init → add → commit）
    ↓
[第3章] リモートに共有（remote add → push）  ← 今ここ
```

次の章からは、チーム開発で必須となる「ブランチ」の使い方を学びます。

---

[← 前の章：第2章 ローカルリポジトリでの基本操作](chapter2.md) | [次の章へ：第4章 ブランチの基本 →](chapter4.md)
