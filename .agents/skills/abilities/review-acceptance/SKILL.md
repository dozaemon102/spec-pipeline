---
name: review-acceptance
description: >-
  Reviews acceptance report for completeness of AC verification and
  traceability. Outputs to reviews/acceptance-review.md. Use when pm-agent
  delegates Phase 6 review, or after create-acceptance.
---

# 最終受入レポートのレビュー

`acceptance.md` の完全性を検査する。文書の修正は行わない。

## 入力

| 入力 | パス |
|------|------|
| 受入レポート | `projects/<project>/docs/features/<feature>/reviews/acceptance.md` |
| 要件定義書 | `projects/<project>/docs/features/<feature>/requirements.md` |
| review-code | `projects/<project>/docs/features/<feature>/reviews/code.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/acceptance-review.md`

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. `requirements.md` の必須 AC・必須 FR を読み込む
2. [assets/review-checklist.md](assets/review-checklist.md) を確認する
3. テンプレートに沿って保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | create-acceptance へ差し戻し（最大1回） |
| 条件付き合格 | Suggestion のみ | 人間ゲートへ |
| 合格 | 指摘なし | 人間ゲートへ |

---

## 次の段階への引き継ぎ

不合格 → [create-acceptance](../create-acceptance/SKILL.md)

合格 → pm-agent の Phase 6 人間ゲートへ
