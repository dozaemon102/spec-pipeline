---
name: review-research
description: >-
  Reviews pre-requirements research documents for citation quality, search
  coverage, and scope appropriateness. Outputs to reviews/research.md. Use when
  pm-agent delegates research review, or after research-pre-requirements.
---

# 事前調査レポートのレビュー

`research.md` の品質を検査する。調査の再実行はしない（指摘のみ）。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 出典・論点・スコープ示唆の検査 | research.md の直接修正 |
| Critical / Suggestion の分類 | Web 検索の再実行 |

---

## 入力

| 入力 | パス |
|------|------|
| 調査レポート | `projects/<project>/docs/features/<feature>/research.md` |
| プロジェクト概要 | `projects/<project>/docs/README.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/research.md`

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. `research.md` を読み込む
2. [assets/review-checklist.md](assets/review-checklist.md) の全項目を確認する
3. テンプレートに沿って保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | 自動差し戻し（research-pre-requirements へ、最大1回） |
| 条件付き合格 | Suggestion のみ | 人間に提示（イントークへ進行可） |
| 合格 | 指摘なし | イントークへ進行 |

---

## 次の段階への引き継ぎ

不合格 → [research-pre-requirements](../research-pre-requirements/SKILL.md) が指摘を反映して再調査
