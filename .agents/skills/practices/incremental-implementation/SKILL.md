---
name: incremental-implementation
description: >-
  Thin vertical slices — implement, test, verify, commit. Use when a change
  touches more than one file during Phase 5 or hotfix.
---

# Incremental Implementation

`.agents/vendor/agent-skills/skills/incremental-implementation/SKILL.md` を最初に読み、手順・検証ゲート・反合理化表に従う。

## spec-pipeline での使い所

- Phase 5: ユースケース単位で backend → frontend を**縦スライス**で完結させる
- 複数ファイルにまたがる変更は一括コミットせず、スライスごとに verify してから次へ

## 併用

- [test-driven-development](../test-driven-development/SKILL.md)
- [git-workflow-and-versioning](../git-workflow-and-versioning/SKILL.md)
