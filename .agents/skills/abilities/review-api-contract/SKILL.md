---
name: review-api-contract
description: >-
  Reviews OpenAPI contract against detailed design and basic design. Outputs
  to reviews/contract.md. Use when pm-agent delegates API contract review,
  or after create-api-contract.
---

# API 契約のレビュー

`contract/openapi.yaml` を詳細設計・基本設計と照合する。契約の修正は行わない。

## 入力

| 入力 | パス |
|------|------|
| OpenAPI | `projects/<project>/docs/features/<feature>/contract/openapi.yaml` |
| 型定義（あれば） | `projects/<project>/docs/features/<feature>/contract/types.ts` |
| 詳細設計書 | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 基本設計書 | `projects/<project>/docs/features/<feature>/basic-design.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/contract.md`

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. 詳細設計の全 API と openapi.yaml を突合する
2. [assets/review-checklist.md](assets/review-checklist.md) を確認する
3. テンプレートに沿って保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | create-api-contract へ差し戻し（最大2回） |
| 条件付き合格 | Suggestion のみ | 人間ゲートへ |
| 合格 | 指摘なし | 人間ゲートへ |

---

## 次の段階への引き継ぎ

不合格 → [create-api-contract](../create-api-contract/SKILL.md)

合格 → pm-agent の Phase 5 へ。implement-from-design は `contract/openapi.yaml` を参照する。
