---
name: review-code
description: >-
  Reviews implemented code against detailed design and requirements.
  Checks API, DB, auth, and layer placement under src/. Outputs to
  reviews/code.md. Use when pm-agent delegates code review, or when
  reviewing implementation against a 詳細設計書.
---

# コードのレビュー

実装コードを詳細設計・要件定義と照合し、指摘を構造化して記録する。コードの修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 設計との整合性検査 | コードの直接修正 |
| 認可・エラーハンドリングの確認 | 設計の変更 |
| 必須テストの存在確認 | AC 最終判定（Phase 6 acceptance が担当） |
| Critical / Suggestion の分類 | 人間ゲートの判断 |

---

## 入力

| 入力 | パス |
|------|------|
| 実装コード | `projects/<project>/src/**/*` |
| 詳細設計書（参照） | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 要件定義書（参照） | `projects/<project>/docs/features/<feature>/requirements.md` |
| 技術スタック（参照） | `projects/<project>/docs/architecture/tech-stack.md` |
| 割当 worker（参照） | pm-agent から指定 |

割当されていない worker のパス（例: devops 未割当時の `src/infra/`）はレビュー対象外とする。

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/code.md`

**言語:** 日本語

[assets/review-output-template.md](assets/review-output-template.md) に従う。

---

## レビュー手順

1. `detailed-design/*.md` と `requirements.md` を読み、期待される API / DB / 認可・受入条件（AC）を把握する
2. `src/` の実装コードを読み込む（割当パスのみ）
3. 割当 worker Skill を参照し、規約違反がないか確認する
4. [assets/review-checklist.md](assets/review-checklist.md) の全項目を確認する
5. 詳細設計ファイルごとの対応状況・テスト存在の概要をテンプレートに記載する
6. テンプレートに沿ってレビュー結果を保存する
7. pm-agent に判定を返す

トレーサビリティ表と AC 達成の**最終判定**は Phase 6 の `reviews/acceptance.md` で行う。本レビューでは設計整合とテストの有無までを検査する。

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | Critical が1件以上 | 自動差し戻し（implement-from-design へ） |
| 条件付き合格 | Suggestion のみ | 人間に提示 |
| 合格 | 指摘なし | 人間に提示 |

---

## 指摘の書き方

- **該当ファイル:** ソースファイルパス（例: `src/backend/routes/inventory.py`）
- **指摘:** 何が問題か（1文）
- **修正案:** implement が直せる具体的内容

```
✅ src/backend/routes/inventory.py: POST /api/v1/inventory にロールチェックがない。詳細設計 stock-update.md §2.1 の ROLE-001 を追加する
❌ 認可が不十分（曖昧）
```

---

## Critical の例

| 状況 | 理由 |
|------|------|
| 設計の API が未実装 | 機能が動作しない |
| 権限なしで更新 API が呼べる | セキュリティ欠陥 |
| 設計にない API がある | スコープ逸脱・レビュー不能 |

## Suggestion の例

| 状況 | 理由 |
|------|------|
| テストがない | 動作はするが回帰リスク |
| 命名が設計と微妙に異なる | 保守性の問題 |

---

## 次の段階への引き継ぎ

- **不合格** → [implement-from-design](../implement-from-design/SKILL.md) が指摘を反映して再実装
- **合格 / 条件付き合格** → pm-agent が Phase 5 人間ゲートへ。承認後 Phase 6 最終受入
