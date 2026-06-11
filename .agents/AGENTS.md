# AGENTS

spec-pipeline のエージェント基盤。要望から実装までを、作成・レビュー・人間承認を繰り返して進める。

## 入口

ユーザーが「○○を作りたい / 作って」と言ったら **`orchestrators/pm-agent`** を起動する。pm-agent が全段階を指揮する。個別 Skill を直接呼ぶこともできる（下表）。

## ルーティング

| ユーザーの意図 | 使う Skill |
|----------------|-----------|
| 新しいものを作りたい（最初から最後まで） | `orchestrators/pm-agent` |
| 要件定義前にドメイン調査したい | `abilities/research-pre-requirements` |
| 事前調査をレビュー | `abilities/review-research` |
| 要件定義書を作る / 直す | `abilities/create-requirement_definition` |
| 要件定義書をレビュー | `abilities/review-requirement_definition` |
| 基本設計書を作る / 直す | `abilities/create-basic_design` |
| 基本設計書をレビュー | `abilities/review-basic_design` |
| 詳細設計書を作る / 直す | `abilities/create-detailed_design` |
| 詳細設計書をレビュー | `abilities/review-detailed_design` |
| 設計からコードを実装 | `abilities/implement-from-design` + `workers/*` |
| 実装の実行検証（テスト・ビルド） | `abilities/verify-run` |
| コードをレビュー | `abilities/review-code` |
| セキュリティレビュー | `abilities/review-security` |
| 計画・設計を壁打ちしたい | `coaches/grill-me` |

## 役割の分類

| 分類 | 場所 | 責務 |
|------|------|------|
| Orchestrator | `skills/orchestrators/` | パイプライン全体の指揮・進行管理・コミット |
| Ability | `skills/abilities/` | 段階ごとの作成・レビュー（成果物を生む / 検査する） |
| Worker | `skills/workers/` | レイヤ別の実装（frontend / backend / infra） |
| Coach | `skills/coaches/` | 壁打ち（パイプライン外） |

## パイプライン

```
Phase 0 初期化
Phase 1 事前調査 → イントーク（人間ゲート）
Phase 2 要件定義
Phase 3 基本設計 → デザインシステム選択（UI あり）
Phase 4 詳細設計
Phase 5 実装 → verify-run → review-code → review-security（条件付き）
Phase 6 最終受入（acceptance）

各段階（Phase 2〜5）: create → review（自動）→ 人間ゲート（次へ / 修正）
```

- レビューの **Critical** は人間に渡す前に自動差し戻し（事前調査 1 回、その他 2 回）
- 人間が「次へ」で承認するたびに git コミット
- **fast-track** モードで小規模案件を簡略化可能

## 成果物の置き場

```
projects/<project>/
├── docs/backlog.md              # 横断バックログ
├── docs/features/<feature>/     # 要件・設計・レビュー・future.md
├── docs/architecture/           # 横断設計
└── src/{frontend,backend,infra}
```

`projects/` は各プロジェクトが独立した git リポジトリ。基盤（`.agents/`）とは別管理。

## ルール

`rules/` の規約は全 Skill に適用される。技術選定は Python 優先・uv、文書は日本語・コードは英語、コミットは Conventional Commits。詳細は各 `.mdc` を参照。

## リソース

`resources/` はエージェントが生成時に参照する素材を置く（人間向けドキュメントの `docs/` とは区別）。

| リソース | パス | 用途 |
|----------|------|------|
| デザインシステム | `resources/design-systems/<name>/DESIGN.md` | UI 生成時のデザイントークン。基本設計承認後に pm-agent が候補提示→人間が選択 |

## 拡張方法

新しい段階・Skill・ルールの追加手順は [docs/authoring-guide.md](../docs/authoring-guide.md) を参照。
