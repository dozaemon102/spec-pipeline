---
name: create-basic_design
description: >-
  Creates a basic design document (基本設計書) from approved requirements.
  Writes feature design to basic-design.md and cross-cutting design to
  docs/architecture/. Use when pm-agent delegates the basic design stage,
  or when creating or revising a 基本設計書.
---

# 基本設計書の作成

承認済み要件定義書をもとに、システム構成・技術選定・機能設計の骨格を文書化する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| モジュール構成・画面概要・データ概念の設計 | API エンドポイントの詳細定義 |
| 技術スタック・システム境界の文書化 | DB スキーマ・カラム型の具体化 |
| 要件 ID とのトレーサビリティ | コードの作成 |

詳細は次段階（create-detailed_design）に委ねる。

---

## 入力

| 入力 | パス |
|------|------|
| 要件定義書 | `projects/<project>/docs/features/<feature>/requirements.md` |
| プロジェクト概要 | `projects/<project>/docs/README.md` |
| 既存横断設計（feature 追加時は必須） | `projects/<project>/docs/architecture/*.md` |
| 前回版（差し戻し時） | `basic-design.md`, `architecture/*.md` |
| 修正指示 | レビュー指摘 or 人間からのフィードバック |

2 つ目以降の feature では、既存 `architecture/` との**矛盾を解消**する。技術スタックを変える場合は理由と影響範囲を `basic-design.md` に明記する。

要件定義書の未決事項（OPN）を可能な限り解消または設計判断として記録する。

---

## 出力

| 成果物 | パス | テンプレート |
|--------|------|-------------|
| 機能基本設計 | `docs/features/<feature>/basic-design.md` | [basic-design-template.md](assets/basic-design-template.md) |
| 技術スタック | `docs/architecture/tech-stack.md` | [tech-stack-template.md](assets/tech-stack-template.md) |
| システム境界 | `docs/architecture/system-context.md` | [system-context-template.md](assets/system-context-template.md) |

**言語:** 日本語（技術名・製品名は英語可）

`architecture/` が未作成の場合はディレクトリごと作成する。既存ファイルがある場合は**追記・更新**し、矛盾があれば統合する。

---

## 執筆手順

1. 要件定義書を読み、FR / NFR / AC を把握する
2. 横断設計（技術スタック・システム境界）を `architecture/` に作成・更新する
3. 機能固有設計を `basic-design.md` に作成する
4. 要件トレーサビリティ（FR → 設計要素）を埋める
5. 未決事項を整理する（詳細設計へ回すものは明示）

### 差し戻し時

- 前回版をベースに、指摘・修正指示を反映した**全体版**を書き直す
- `architecture/` と `basic-design.md` の両方を整合させる

---

## 記述ガイドライン

### モジュール・画面

- 要件 ID（FR-xxx）への参照を必ず付ける
- 画面は「何ができるか」まで。ワイヤーフレーム・項目定義の詳細は書かない

### データ概要

- エンティティ名と概念レベルの属性のみ
- 型・制約・リレーションの詳細は詳細設計へ

```
✅ 在庫（商品 ID、数量、更新日時）
❌ inventories テーブル: quantity INTEGER NOT NULL
```

### 技術選定

- 選定理由を1行以上書く
- 要件の非機能要件（NFR）と対応づける

### システム境界

- 本システムの内外を明確にする
- 外部連携のプロトコル詳細は書かない（「REST で連携」程度まで）

### 認証・認可

- 要件定義の権限マトリクスを実現する方針を設計する
- 認証方式・権限モデル（例: RBAC）・チェック実施層を決める
- トークン仕様・エンドポイント単位の細かい制御は詳細設計へ
- アクセス制御が不要な場合は「該当なし」と明記する

---

## 品質チェック（自己確認）

- [ ] 全必須 FR に設計要素が対応している
- [ ] 要件定義のロール・権限が認証・認可設計に反映されている
- [ ] API・DB の詳細に踏み込んでいない
- [ ] `architecture/` と `basic-design.md` に矛盾がない
- [ ] 既存 `architecture/` がある場合、追記・更新が整合している（feature 追加時）
- [ ] 要件定義の未決事項が処理または引き継がれている
- [ ] 日本語で統一されている

---

## 次の段階への引き継ぎ

[review-basic_design](../review-basic_design/SKILL.md) でレビューされる。

人間承認後、pm-agent が [create-detailed_design](../create-detailed_design/SKILL.md) に委譲する。
