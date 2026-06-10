# spec-pipeline 拡張ガイド

このリポジトリ（`.agents/`）に Skill・ルールを追加 / 変更する人向けのガイド。
生成物（`projects/`）ではなく、**パイプライン基盤そのもの**を保守する際に読む。

## Skill の置き場所

| 種類 | パス | 役割 |
|------|------|------|
| Orchestrator | `.agents/skills/orchestrators/` | パイプライン全体の指揮（pm-agent） |
| Ability | `.agents/skills/abilities/` | 段階ごとの作成・レビュー |
| Worker | `.agents/skills/workers/` | レイヤ別の実装 |
| Coach | `.agents/skills/coaches/` | 壁打ち等（パイプライン外） |

Skill 名は `SKILL.md` を含むフォルダ名。カテゴリフォルダは整理用で、名前には影響しない。

## SKILL.md の規約

- frontmatter に `name`（小文字・ハイフン/アンダースコア）と `description`（WHAT + WHEN、英語）を書く
- 本体は簡潔に。テンプレート・チェックリストなど長い素材は `assets/` に分離する
- 参照は1階層まで（`assets/foo.md` への直リンク）

```
<skill-name>/
├── SKILL.md          # 手順・判定ルール（簡潔に）
└── assets/           # テンプレート・チェックリスト
```

## 新しい段階（create / review ペア）を足す手順

1. `abilities/create-<stage>/` と `abilities/review-<stage>/` を作る
2. それぞれに `SKILL.md` と `assets/`（テンプレート / チェックリスト）を用意する
3. `create` は入力・出力パス・手順・自己チェックを定義する
4. `review` は Critical / Suggestion のチェックリストと判定ルールを定義する
5. `orchestrators/pm-agent/SKILL.md` のパイプライン表とフローに段階を追加する
6. 前後の段階の「次の段階への引き継ぎ」リンクを更新する

## 作成 / レビューの分離原則

| | 作成（create / implement） | レビュー（review） |
|--|---------------------------|--------------------|
| 役割 | 成果物を書く | 成果物を検査する |
| 出力 | 設計書 / コード | `reviews/` 配下の指摘 |
| 修正 | する | **しない**（指摘のみ） |

## レビュー判定の共通ルール

- **Critical**: その段階を進められない欠陥 → pm-agent が自動差し戻し（最大2回）
- **Suggestion**: 改善余地はあるが進行可能 → 人間に提示
- 判定は `合格 / 条件付き合格 / 不合格` の3値

## ルールの追加

- `.agents/rules/*.mdc` に置く
- 横断的なら `alwaysApply: true`、特定ファイルなら `globs` を指定する
- 1ファイル1関心。簡潔に保つ

## リソースの追加

エージェントが生成時に参照する素材（人間向けドキュメントではないもの）は `.agents/resources/` に置く。

- 例: `resources/design-systems/<name>/DESIGN.md`（UI 生成時のデザイントークン）
- 「人間が読む保守ドキュメント」は `docs/`、「エージェントが消費する素材」は `.agents/resources/` と使い分ける
- 参照する Skill 側の SKILL.md に、既知パスと適用手順を明記する

## 設計境界（各段階で書くこと / 書かないこと）

| 段階 | 書く | 書かない |
|------|------|----------|
| 要件定義 | 何を / 誰が（ロール×操作） | どう実装するか |
| 基本設計 | 構成・技術選定・認可方針 | API/DB の詳細 |
| 詳細設計 | API・DB・エラー・認可の具体 | コード |
| 実装 | コード | 設計変更 |

この境界はレビューの Critical 判定に直結する。段階を追加するときも同じ粒度感を保つ。
