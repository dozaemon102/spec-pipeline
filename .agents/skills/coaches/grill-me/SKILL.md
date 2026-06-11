---
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
---

## 入力（pm-agent から委譲時）

- `projects/<project>/docs/features/<feature>/research.md` が存在する場合は**必ず読み**、セクション 9（ヒアリング論点）とセクション 10（スコープ示唆）を優先して深掘りする
- pm-agent が research パスを渡した場合はそれを使う
- 調査で既に示唆がある論点は「調査では〇〇とあるが、最終判断は？」と確認する

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.
