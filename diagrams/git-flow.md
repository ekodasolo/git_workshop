# Git Flowの全体像（5つのブランチの役割図）

## 概要

Git Flowで使われる5種類のブランチの関係と役割を示す図です。第4章のブランチ戦略の説明で使用します。

## 図

```mermaid
gitGraph
    commit id: "v1.0" tag: "v1.0"
    branch develop
    checkout develop
    commit id: "dev-1"
    branch feature/sides
    checkout feature/sides
    commit id: "sides追加"
    commit id: "README更新"
    checkout develop
    merge feature/sides id: "feature完了"
    commit id: "dev-2"
    branch release/v1.1
    checkout release/v1.1
    commit id: "バグ修正"
    checkout main
    merge release/v1.1 id: "v1.1" tag: "v1.1"
    checkout develop
    merge release/v1.1 id: "release反映"
    checkout main
    branch hotfix/urgent
    commit id: "緊急修正"
    checkout main
    merge hotfix/urgent id: "v1.1.1" tag: "v1.1.1"
    checkout develop
    merge hotfix/urgent id: "hotfix反映"
```

## テキスト版（スライド用）

```
main        ──●──────────────────────●──●── (本番リリース)
               \                    ↑   ↑
release         \            ──●──●/    |   (リリース準備)
                 \          /           |
develop     ──●───●──●──●──●──●─────●──/    (開発の統合)
                   \      ↑    \    ↑
feature             ●──●─/      ●──/        (機能開発)
                                ↑
hotfix      ────────────────── ●──          (緊急修正)
```

## 各ブランチの役割

| ブランチ | 役割 | 寿命 |
|---|---|---|
| main | 本番リリースの状態を保持する | 永続 |
| develop | 開発中の最新状態を統合する | 永続 |
| feature | 個別の機能を開発する | 一時的（マージ後に削除） |
| release | リリース前の最終調整を行う | 一時的（マージ後に削除） |
| hotfix | 本番の緊急バグを修正する | 一時的（マージ後に削除） |

## 補足

- Git Flowは大規模プロジェクト向けの戦略であることを伝える
- 講座ではmain + featureの2つに絞って体験すると範囲を宣言する
- GitHub FlowやGitLab Flowといった軽量な戦略もあることに触れる
