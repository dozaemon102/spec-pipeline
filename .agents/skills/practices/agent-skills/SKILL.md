---
name: agent-skills
description: >-
  Routes work to vendored addyosmani/agent-skills workflows that complement
  spec-pipeline. Use when implementation, debugging, UI, performance, git,
  or shipping practices beyond pm-agent abilities are needed.
---

# Agent Skills（vendor）

[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) を `.agents/vendor/agent-skills/` に取り込んだ。spec-pipeline（要件→設計→実装）の**補完**として使う。pm-agent の段階 Skill を置き換えない。

## 使い分け

| spec-pipeline | vendor で補う場面 | vendor パス |
|---------------|------------------|-------------|
| pm-agent Phase 0–4 | （使わない — 自前の create/review を優先） | — |
| implement-from-design | TDD・小さな縦スライス・公式ドキュメント準拠 | `practices/test-driven-development` 等 |
| verify-run | ブラウザ実機検証 | `practices/browser-testing-with-devtools` |
| review-code / review-security | 追加のレビュー軸・OWASP 深掘り | vendor `skills/code-review-and-quality` 等 |
| hotfix | デバッグ手順 | `practices/debugging-and-error-recovery` |
| frontend-worker | UI/アクセシビリティ | `practices/frontend-ui-engineering` |
| devops-worker | CI/CD・観測性・リリース | vendor `skills/ci-cd-and-automation` 等 |
| 全 Phase | Git 作法 | `practices/git-workflow-and-versioning` |

## practices ラッパー（Cursor から直接呼べる）

| Skill | 用途 |
|-------|------|
| [test-driven-development](../test-driven-development/SKILL.md) | Red-Green-Refactor、テストピラミッド |
| [incremental-implementation](../incremental-implementation/SKILL.md) | 縦スライス実装・小さなコミット |
| [debugging-and-error-recovery](../debugging-and-error-recovery/SKILL.md) | 再現→局所化→修正→ガード |
| [git-workflow-and-versioning](../git-workflow-and-versioning/SKILL.md) | trunk-based、原子コミット |
| [browser-testing-with-devtools](../browser-testing-with-devtools/SKILL.md) | DevTools MCP によるブラウザ検証 |
| [frontend-ui-engineering](../frontend-ui-engineering/SKILL.md) | コンポーネント・a11y・デザインシステム |
| [performance-optimization](../performance-optimization/SKILL.md) | 計測ファーストの最適化 |
| [doubt-driven-development](../doubt-driven-development/SKILL.md) | 高リスク変更の adversarial レビュー |
| [context-engineering](../context-engineering/SKILL.md) | セッション・コンテキスト管理 |

## vendor 直参照（ラッパーなし）

`.agents/vendor/agent-skills/skills/<name>/SKILL.md` を Read して従う。

| Skill | 用途 |
|-------|------|
| `source-driven-development` | フレームワーク公式ドキュメント準拠 |
| `api-and-interface-design` | API 契約設計の深掘り（create-api-contract 補完） |
| `security-and-hardening` | OWASP・認可・シークレット（review-security 補完） |
| `code-simplification` | 動作を保った簡素化 |
| `observability-and-instrumentation` | ログ・メトリクス・トレース |
| `ci-cd-and-automation` | パイプライン・feature flag |
| `shipping-and-launch` | 本番リリースチェックリスト |
| `documentation-and-adrs` | ADR・API ドキュメント |
| `deprecation-and-migration` | 廃止・移行 |

## エージェントペルソナ・チェックリスト

- ペルソナ: `.agents/vendor/agent-skills/agents/`（code-reviewer, test-engineer, security-auditor, web-performance-auditor）
- チェックリスト: `.agents/vendor/agent-skills/references/`（testing, security, performance, accessibility）

## 更新

```bash
git submodule update --remote .agents/vendor/agent-skills
```
