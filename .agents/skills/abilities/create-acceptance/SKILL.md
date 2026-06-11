---
name: create-acceptance
description: >-
  Creates final acceptance report with AC achievement, traceability, and
  future FR migration. Writes reviews/acceptance.md and future.md. Use when
  pm-agent delegates Phase 6 acceptance, or after implementation approval.
---

# 最終受入レポートの作成

実装承認後、要件の達成状況を突合し受入レポートを文書化する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| AC 達成・トレーサビリティの突合 | コードの修正 |
| `acceptance.md` の作成 | 設計の変更 |
| 将来 FR の `future.md` 移管 | 人間ゲートの判断 |

---

## 入力

| 入力 | パス |
|------|------|
| 要件定義書 | `projects/<project>/docs/features/<feature>/requirements.md` |
| 詳細設計書 | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 実装コード | `projects/<project>/src/**/*` |
| verify-run | `projects/<project>/docs/features/<feature>/reviews/verify-run.md` |
| review-code | `projects/<project>/docs/features/<feature>/reviews/code.md` |
| 修正指示 | 人間からのフィードバック（差し戻し時） |

---

## 出力

| 成果物 | パス | テンプレート |
|--------|------|-------------|
| 受入レポート | `reviews/acceptance.md` | [assets/acceptance-template.md](assets/acceptance-template.md) |
| 将来要件 | `future.md`（初回作成時） | [assets/future-template.md](assets/future-template.md) |
| バックログ更新 | `docs/backlog.md` | 将来 FR のリンクを追記 |

**言語:** 日本語

---

## 作成手順

1. 必須 AC ごとに、テスト・実装で達成されているか検証する
2. 必須 FR のトレーサビリティ（要件 → 詳細設計 → コード → テスト）を表にする
3. 権限マトリクスを実装・テストと突合する
4. 優先度「将来」の FR を `future.md` に移管し、`backlog.md` を更新する
5. `reviews/code.md` の Critical が未解消なら受入判定を **不合格** とする
6. テンプレートに沿って `acceptance.md` を保存する

### 差し戻し時

- 指摘を反映した全体版を書き直す

---

## 品質チェック（自己確認）

- [ ] 必須 AC がすべて検証されている
- [ ] 必須 FR に断絶がない
- [ ] 権限マトリクスが検証されている
- [ ] 将来 FR が `future.md` に移管されている（該当時）
- [ ] verify-run・review-code との整合が記載されている

---

## 次の段階への引き継ぎ

[review-acceptance](../review-acceptance/SKILL.md) でレビューされる。

人間承認後、pm-agent が完了報告する。
