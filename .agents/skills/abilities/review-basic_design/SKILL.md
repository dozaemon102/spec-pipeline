---
name: review-basic_design
description: >-
  Reviews basic design documents (基本設計書) including feature design and
  architecture docs for consistency with requirements. Outputs to
  reviews/basic-design.md. Use when pm-agent delegates basic design review,
  or when reviewing a 基本設計書.
---

# 基本設計書のレビュー

基本設計書と横断設計（architecture/）を検査し、要件定義との整合性を確認する。文書の修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 基本設計・architecture の品質検査 | 設計書の直接修正 |
| 要件定義との整合性確認 | 詳細設計の妥当性判断 |
| Critical / Suggestion の分類 | 人間ゲートの判断 |

---

## 入力

| 入力 | パス |
|------|------|
| 機能基本設計 | `projects/<project>/docs/features/<feature>/basic-design.md` |
| 技術スタック | `projects/<project>/docs/architecture/tech-stack.md` |
| システム境界 | `projects/<project>/docs/architecture/system-context.md` |
| 要件定義書（参照） | `projects/<project>/docs/features/<feature>/requirements.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/basic-design.md`

**言語:** 日本語

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. 入力ファイルをすべて読み込む
2. 要件定義書と照合し、FR / ロール・権限の反映を確認する
3. [assets/review-checklist.md](assets/review-checklist.md) の全項目を確認する
4. 指摘を Critical / Suggestion に分類する
5. テンプレートに沿ってレビュー結果を保存する
6. pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | 自動差し戻し（create-basic_design へ） |
| 条件付き合格 | Suggestion のみ | 人間に提示 |
| 合格 | 指摘なし | 人間に提示 |

---

## 指摘の書き方

- **該当ファイル / 箇所:** ファイル名 + セクション名（例: `basic-design.md §6`, `tech-stack.md §2`）
- **指摘:** 何が問題か（1文）
- **修正案:** create が直せる具体的内容

```
✅ basic-design.md §6: 要件の ROLE-002 が認可設計に未反映。§6.3 に閲覧者ロールの許可操作を追加する
❌ 認可設計が不十分（曖昧）
```

---

## Critical の例

| 状況 | 理由 |
|------|------|
| 必須 FR が設計にない | 詳細設計・実装の根拠がない |
| API パス・テーブル定義が書かれている | 基本設計と詳細設計の境界が崩れる |
| 要件の権限と認可設計が矛盾 | 実装時にセキュリティ欠陥の原因になる |

## Suggestion の例

| 状況 | 理由 |
|------|------|
| トレーサビリティ表が薄い | 追跡は可能だが、後工程で漏れやすい |
| 認可のチェック層が未定 | 詳細設計で決められるが、早めに決めるとよい |

---

## 次の段階への引き継ぎ

- **不合格** → [create-basic_design](../create-basic_design/SKILL.md) が指摘を反映して再作成
- **合格 / 条件付き合格** → pm-agent が人間ゲートへ。承認後 [create-detailed_design](../create-detailed_design/SKILL.md) へ
