---
name: debugging-and-error-recovery
description: >-
  Five-step triage — reproduce, localize, reduce, fix, guard. Use when tests
  fail, builds break, or behavior is unexpected, especially in hotfix mode.
---

# Debugging and Error Recovery

`.agents/vendor/agent-skills/skills/debugging-and-error-recovery/SKILL.md` を最初に読み、手順・検証ゲート・反合理化表に従う。

## spec-pipeline での使い所

- verify-run 不合格 → implement-from-design 差し戻し時
- pm-agent hotfix（`fix/` ブランチ）モード
- review-code の Critical 修正ループ
