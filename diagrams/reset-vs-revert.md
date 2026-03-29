# reset vs revertの違いを示す図

## 概要

`git reset` と `git revert` の根本的な違い（履歴を書き換える vs 履歴に追記する）を示す図です。第6章で使用します。

## 図

```mermaid
graph TD
    subgraph "git reset（履歴を書き換える）"
        direction LR
        R1["A"] --> R2["B"] --> R3["C"]
        R3 -. "reset" .-> R2
        R4["結果: A → B（Cは消える）"]
    end

    subgraph "git revert（履歴に追記する）"
        direction LR
        V1["A"] --> V2["B"] --> V3["C"] --> V4["C'<br>(Cの打ち消し)"]
        V5["結果: A → B → C → C'（Cは残る）"]
    end
```

## テキスト版（スライド用）

```
【git reset】 履歴を書き換える
  Before:  A → B → C
  After:   A → B          ← Cが履歴から消える
  用途:    プッシュ前のコミットを取り消す

【git revert】 履歴に追記する
  Before:  A → B → C
  After:   A → B → C → C' ← Cは残り、打ち消しコミットC'が追加される
  用途:    プッシュ後のコミットを安全に取り消す
```

## 使い分けの判断基準

```mermaid
flowchart TD
    Q["取り消したいコミットは<br>プッシュ済み？"]
    Q -- "いいえ（プッシュ前）" --> RESET["git reset を使う<br>履歴を書き換えてOK"]
    Q -- "はい（プッシュ済み）" --> REVERT["git revert を使う<br>履歴を壊さず安全に打ち消す"]
```

## 補足

- reset は「なかったことにする」、revert は「打ち消す記録を残す」というイメージで説明する
- プッシュ済みかどうかが最大の判断基準であることを強調する
- reset をプッシュ済みのコミットに使うとチームメンバーとの履歴が食い違う危険がある点を注意喚起する
