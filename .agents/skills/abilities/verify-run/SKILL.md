---
name: verify-run
description: >-
  Runs tests, build, and smoke checks on implemented code before code review.
  Outputs to reviews/verify-run.md. Use when pm-agent delegates verify-run
  after implementation, or before review-code.
---

# 実行検証（verify-run）

実装後にテスト・ビルド・起動を実行し、動作可能性を確認する。コードの修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| テスト・ビルド・起動の実行 | コードの直接修正 |
| 結果の記録 | 設計整合の詳細レビュー（review-code に委譲） |

---

## 入力

| 入力 | パス |
|------|------|
| 実装コード | `projects/<project>/src/**/*` |
| 技術スタック | `projects/<project>/docs/architecture/tech-stack.md` |
| 要件定義（AC 参照） | `projects/<project>/docs/features/<feature>/requirements.md` |
| 割当 worker | pm-agent から指定 |

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/reviews/verify-run.md`

[assets/verify-run-output-template.md](assets/verify-run-output-template.md) に従う。

---

## 検証手順

1. `tech-stack.md` を読み、使用ツールを確認する
2. 次を**実際に実行**する（推測で合格にしない）

| 領域 | コマンド例 | 必須 |
|------|-----------|------|
| backend テスト | `cd projects/<project>/src/backend && uv run pytest` | backend 割当時 |
| backend カバレッジ | `uv run pytest --cov` | Suggestion 対象 |
| frontend テスト | `npm test` / `pnpm test`（package.json に従う） | frontend 割当時 |
| frontend ビルド | `npm run build` | frontend 割当時 |
| 起動確認 | README または infra の手順で起動 | 可能な場合 |

3. 失敗したコマンドはログの要点を記録する
4. テンプレートに結果を保存し、pm-agent に判定を返す

### 判定の返し方

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | 必須コマンドが失敗 | implement-from-design へ差し戻し（最大2回） |
| 条件付き合格 | 必須は通過、ビルド/起動は未実行または警告 | review-code へ進行（理由を明記） |
| 合格 | 必須コマンドすべて成功 | review-code へ進行 |

実行環境が整っていない場合（依存未インストール等）は、実行した範囲を記録し**条件付き合格**とする。未実行理由を明記する。

---

## 次の段階への引き継ぎ

不合格 → [implement-from-design](../implement-from-design/SKILL.md)

合格 / 条件付き合格 → [review-code](../review-code/SKILL.md)
