---
name: review-security
description: >-
  Reviews implemented code for security risks against requirements and
  project security rules. Outputs to reviews/security.md. Use when pm-agent
  delegates conditional security review after code implementation, or when
  auth, PII, or external APIs are involved.
---

# セキュリティレビュー

認証・認可・機密データ・外部連携に関する実装を検査する。コードの修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| セキュリティ観点の検査 | コードの直接修正 |
| Critical / Suggestion の分類 | 一般コード品質（review-code に委譲） |

`.agents/rules/security-secrets.mdc` を参照する。

---

## 入力

| 入力 | パス |
|------|------|
| 実装コード | `projects/<project>/src/**/*`（割当パスのみ） |
| 要件定義 | `projects/<project>/docs/features/<feature>/requirements.md` |
| 詳細設計 | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| verify-run 結果 | `projects/<project>/docs/features/<feature>/reviews/verify-run.md` |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/security.md`

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. 入力を読み、認証・認可・データ保護・外部連携の有無を把握する
2. [assets/review-checklist.md](assets/review-checklist.md) の全項目を確認する
3. テンプレートに沿って保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | implement-from-design へ差し戻し |
| 条件付き合格 | Suggestion のみ | 人間ゲートへ |
| 合格 | 指摘なし | 人間ゲートへ |

該当機能がない場合（認証なし・データ公開のみ）は「該当なし」と記し合格とする。

---

## 次の段階への引き継ぎ

不合格 → [implement-from-design](../implement-from-design/SKILL.md)

合格 → pm-agent の Phase 5 人間ゲートへ
