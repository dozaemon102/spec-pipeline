---
name: git-workflow-and-versioning
description: >-
  Trunk-based development, atomic commits, and change sizing. Use for any code
  change in spec-pipeline projects.
---

# Git Workflow and Versioning

`.agents/vendor/agent-skills/skills/git-workflow-and-versioning/SKILL.md` を最初に読み、手順・検証ゲート・反合理化表に従う。

## spec-pipeline での使い所

- pm-agent が人間ゲート承認後にコミットする際
- Phase 5 実装中の原子コミット（~100 行目安）
- `projects/<project>/` 各リポジトリでのブランチ運用

## 注意

- 本ワークスペースの Conventional Commits 規約（`.agents/rules/`）を優先する
- vendor の trunk-based 原則と矛盾しない範囲で適用する
