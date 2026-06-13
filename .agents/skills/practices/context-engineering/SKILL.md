---
name: context-engineering
description: >-
  Feed agents the right information at the right time — rules, context packing,
  MCP. Use when starting a session, switching tasks, or output quality drops.
---

# Context Engineering

`.agents/vendor/agent-skills/skills/context-engineering/SKILL.md` を最初に読み、手順・検証ゲート・反合理化表に従う。

## spec-pipeline での使い所

- pm-agent セッション開始時: `.pipeline-state.md` と当該 feature の docs を先に読む
- Phase 切り替え時: 前段階の成果物だけをコンテキストに載せ、不要な履歴を捨てる
