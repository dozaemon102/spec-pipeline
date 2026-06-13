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
| 段階横断の整合をレビュー | `abilities/review-consistency` |
| 要件定義書を作る / 直す | `abilities/create-requirement_definition` |
| 要件定義書をレビュー | `abilities/review-requirement_definition` |
| 基本設計書を作る / 直す | `abilities/create-basic_design` |
| 基本設計書をレビュー | `abilities/review-basic_design` |
| 詳細設計書を作る / 直す | `abilities/create-detailed_design` |
| 詳細設計書をレビュー | `abilities/review-detailed_design` |
| API 契約（OpenAPI）を作る | `abilities/create-api-contract` |
| API 契約をレビュー | `abilities/review-api-contract` |
| 設計からコードを実装 | `abilities/implement-from-design` + `workers/*` |
| 実装の実行検証（テスト・ビルド） | `abilities/verify-run` |
| コードをレビュー | `abilities/review-code` |
| セキュリティレビュー | `abilities/review-security` |
| 最終受入レポートを作る | `abilities/create-acceptance` |
| 最終受入をレビュー | `abilities/review-acceptance` |
| 計画・設計を壁打ちしたい | `coaches/grill-me` |
| 不具合修正（hotfix） | `orchestrators/pm-agent`（`fix/` + hotfix-guide） |
| TDD・縦スライス実装・Git 作法 | `practices/test-driven-development` 等（[agent-skills](skills/practices/agent-skills/SKILL.md) 参照） |
| ブラウザ実機検証 | `practices/browser-testing-with-devtools` |
| UI / a11y 深掘り | `practices/frontend-ui-engineering` |
| 性能・デバッグ・高リスク監査 | `practices/performance-optimization` 等 |

## 役割の分類

| 分類 | 場所 | 責務 |
|------|------|------|
| Orchestrator | `skills/orchestrators/` | パイプライン全体の指揮・進行管理・コミット |
| Ability | `skills/abilities/` | 段階ごとの作成・レビュー（成果物を生む / 検査する） |
| Worker | `skills/workers/` | レイヤ別の実装（frontend / backend / infra） |
| Coach | `skills/coaches/` | 壁打ち（パイプライン外） |
| Practice | `skills/practices/` | [agent-skills](skills/practices/agent-skills/SKILL.md) 由来の実装・検証ワークフロー（パイプライン補完） |

## パイプライン

```
Phase 0 初期化
Phase 1 事前調査 → イントーク（人間ゲート）
Phase 2 要件定義 → review-consistency
Phase 3 基本設計 → デザインシステム選択（UI あり）→ review-consistency
Phase 4 詳細設計 → review-consistency
Phase 4.5 API 契約（OpenAPI）
Phase 5 実装 → verify-run → review-code → review-security（条件付き）
Phase 6 最終受入（create-acceptance → review-acceptance）

各段階: create → review（自動）→ 人間ゲート（次へ / 修正）
fix/ ブランチ: hotfix モード（短縮フロー）
```

- レビューの **Critical** は人間に渡す前に自動差し戻し（事前調査・横断整合・受入は最大1回、その他2回）
- 人間が「次へ」で承認するたびに git コミット
- **fast-track** モードで小規模案件を簡略化可能

## 成果物の置き場

```
projects/<project>/
├── docs/backlog.md
├── docs/features/<feature>/   # 要件・設計・契約・レビュー
├── docs/architecture/
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

## 外部スキル（agent-skills）

[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) を `.agents/vendor/agent-skills/` に submodule 取り込み済み。要件〜設計の段階は自前 Skill を優先し、**実装・検証・デバッグ・Git** は `skills/practices/` 経由で vendor ワークフローを補完する。一覧と使い分けは [practices/agent-skills](skills/practices/agent-skills/SKILL.md)。

更新: `git submodule update --remote .agents/vendor/agent-skills`

## 拡張方法

新しい段階・Skill・ルールの追加手順は [docs/authoring-guide.md](../docs/authoring-guide.md) を参照。
