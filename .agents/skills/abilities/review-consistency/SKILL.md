---
name: review-consistency
description: >-
  Cross-stage consistency review between research, requirements, basic design,
  and detailed design documents. Outputs to reviews/consistency/. Use when
  pm-agent runs pre-gate checks for phases 2-4, or when validating document
  alignment across pipeline stages.
---

# 段階横断整合レビュー

複数段階の成果物間のズレを検査する。文書の修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 前段階成果物との整合検査 | 成果物の直接修正 |
| Critical / Suggestion の分類 | 単一段階の品質（review-* に委譲） |

---

## 入力

pm-agent が **対象 Phase** を指定する。

| 対象 Phase | 比較する成果物 |
|------------|---------------|
| 2（要件定義） | `research.md` ↔ `requirements.md` |
| 3（基本設計） | `requirements.md` ↔ `basic-design.md` + `architecture/*` |
| 4（詳細設計） | `basic-design.md` ↔ `detailed-design/*.md` + `requirements.md`（FR カバー） |

共通参照: `projects/<project>/docs/README.md`

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/consistency/<stage>.md`

`<stage>` は `requirements` / `basic-design` / `detailed-design`

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. 対象 Phase に応じた入力ファイルをすべて読み込む
2. [assets/review-checklist.md](assets/review-checklist.md) の該当セクションを確認する
3. テンプレートに沿って保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | 自動差し戻し（当該 create へ、最大1回） |
| 条件付き合格 | Suggestion のみ | 人間ゲートへ |
| 合格 | 指摘なし | 人間ゲートへ |

自動差し戻しは **最大 1 回**（横断レビューはコストが高いため）。

---

## 次の段階への引き継ぎ

不合格 → 当該 Phase の create Skill へ（指摘を修正指示に含める）

合格 → pm-agent が人間ゲートへ進める
