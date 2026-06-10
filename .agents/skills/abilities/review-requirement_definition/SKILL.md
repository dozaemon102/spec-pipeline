---
name: review-requirement_definition
description: >-
  Reviews a requirements definition document (要件定義書) for completeness,
  consistency, and testability. Outputs review results to reviews/requirements.md.
  Use when pm-agent delegates requirements review, or when reviewing a 要件定義書.
---

# 要件定義書のレビュー

作成された要件定義書を検査し、指摘を構造化して記録する。文書の修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 要件定義書の品質検査 | 要件定義書の直接修正 |
| Critical / Suggestion の分類 | 基本設計の妥当性判断 |
| レビュー結果の記録 | 人間ゲートの判断 |

---

## 入力

| 入力 | パス |
|------|------|
| レビュー対象 | `projects/<project>/docs/features/<feature>/requirements.md` |
| コンテキスト | `projects/<project>/docs/README.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/requirements.md`

**言語:** 日本語

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. 入力ファイルを読み込む
2. [assets/review-checklist.md](assets/review-checklist.md) の全項目を確認する
3. 指摘を Critical / Suggestion に分類する
4. テンプレートに沿ってレビュー結果を保存する
5. pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | 自動差し戻し（create-requirement_definition へ） |
| 条件付き合格 | Suggestion のみ | 人間に提示 |
| 合格 | 指摘なし | 人間に提示 |

---

## 指摘の書き方

- **該当箇所:** セクション名 or 要件 ID（例: `FR-003`, `8. 受入条件`）
- **指摘:** 何が問題か（1文）
- **修正案:** create が直せる具体的内容

```
✅ FR-002 に対応する AC がない。AC-003 として「〜のとき、〜であること」を追加する
❌ 受入条件が不足しています（曖昧）
```

---

## Critical の例

| 状況 | 理由 |
|------|------|
| スコープに「含まない」がない | 範囲が不明確で基本設計に進めない |
| FR が実装詳細を含む | 要件と設計の境界が崩れる |
| 必須 FR に AC がない | 完成の定義がない |

## Suggestion の例

| 状況 | 理由 |
|------|------|
| NFR が空 | 基本設計で補完可能だが、早めに書くとよい |
| 用語が未定義 | 誤解の余地があるが、文脈で理解可能 |

---

## 次の段階への引き継ぎ

- **不合格** → [create-requirement_definition](../create-requirement_definition/SKILL.md) が指摘を反映して再作成
- **合格 / 条件付き合格** → pm-agent が人間ゲートへ。承認後 [create-basic_design](../create-basic_design/SKILL.md) へ
