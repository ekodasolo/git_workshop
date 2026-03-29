# Gitの基本フロー全体図

## 概要

Gitの基本操作の全体像を1枚にまとめた図です。第9章のまとめおよび講座全体の振り返りで使用します。

## 図

```mermaid
flowchart TD
    subgraph LOCAL["ローカル"]
        direction TB
        INIT["git init<br>リポジトリを作成"]
        EDIT["ファイルを編集"]
        ADD["git add<br>ステージングに登録"]
        COMMIT["git commit<br>履歴に記録"]

        INIT --> EDIT
        EDIT --> ADD
        ADD --> COMMIT
        COMMIT --> EDIT
    end

    subgraph REMOTE["リモート（CodeCommit）"]
        direction TB
        REPO["リモートリポジトリ"]
    end

    COMMIT -- "git push<br>ローカル → リモート" --> REPO
    REPO -- "git fetch + merge<br>リモート → ローカル" --> EDIT

    subgraph UNDO["やり直し操作"]
        direction TB
        RESTORE["git restore<br>ワークツリーの変更取り消し"]
        RESTORE_S["git restore --staged<br>ステージング取り消し"]
        AMEND["git commit --amend<br>直前のコミット修正"]
        RESET["git reset<br>コミット取り消し（プッシュ前）"]
        REVERT["git revert<br>コミット打ち消し（プッシュ後）"]
    end

    EDIT -. "まだaddしていない" .-> RESTORE
    ADD -. "addしたけど取り消したい" .-> RESTORE_S
    COMMIT -. "コミットを直したい" .-> AMEND
    COMMIT -. "コミットを取り消したい" .-> RESET
    REPO -. "プッシュ済みを取り消したい" .-> REVERT

    subgraph BRANCH["ブランチ運用"]
        direction LR
        CREATE["git switch -c<br>ブランチ作成"]
        WORK["作業 → add → commit"]
        MERGE["git switch main<br>git merge<br>マージ"]
        DELETE["git branch -d<br>ブランチ削除"]

        CREATE --> WORK --> MERGE --> DELETE
    end
```

## テキスト版（スライド用）

```
[初期設定]
  git config --global user.name / user.email

[ローカルでの基本サイクル]
  git init → ファイル編集 → git add → git commit
                ↑                        |
                └────────────────────────┘

[リモートとの連携]
  git push          ローカル → リモート
  git fetch + merge リモート → ローカル

[ブランチ運用]
  git switch -c → 作業 → add → commit → git switch main → git merge → git branch -d

[やり直し操作]
  プッシュ前: git restore / git restore --staged / git commit --amend / git reset
  プッシュ後: git revert
```

## 補足

- この図は講座全体の「地図」として、第1章の冒頭で軽く見せ、第9章のまとめで振り返る使い方を想定
- 受講者に「今、全体のどこをやっているか」を意識させるために各章の冒頭で該当部分をハイライトしてもよい
