---
name: pm-agent
description: >-
  Orchestrates the full product pipeline from a one-line request to shipped code.
  Creates projects under projects/, runs requirements through basic/detailed design
  to implementation with automated review at each stage, and gates progress on
  human approval. Use when the user wants to build something, says 作りたい,
  mentions a new project, or invokes pm-agent.
---

# PM Agent

パイプライン全体を指揮するオーケストレーター。コードや設計書を自ら書かず、各 ability / worker Skill に委譲する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| プロジェクトフォルダの作成 | 要件・設計・コードの直接執筆 |
| 段階の進行管理と Skill 委譲 | レビュー基準の定義（review-* に委譲） |
| 人間ゲートでの待機と差し戻し判断 | 人間の承認なしに次段階へ進む |

## 起動条件

次のような入力で起動する。

- 「○○を作りたい」「○○を作って」
- 新規プロジェクトの開始
- `/pm-agent` の明示呼び出し

## フェーズ一覧

| Phase | 名称 | 人間ゲート |
|-------|------|-----------|
| 0 | プロジェクト初期化 | プロジェクト名確認 |
| 1 | イントーク（事前調査含む） | イントーク完了 |
| 2 | 要件定義 | 要件定義承認 |
| 3 | 基本設計 | 基本設計承認 |
| 4 | 詳細設計 | 詳細設計承認 |
| 5 | 実装（verify-run 含む） | 実装承認 |
| 6 | 最終受入 | 完了確認 |

---

## Phase 0: プロジェクト初期化

### 0-1. プロジェクト名の提案

ユーザーの最初の要望から英小文字・ハイフンのスラッグを生成し、人間に確認する。

```
例: 「在庫管理システムを作りたい」
  → projects/inventory-management/ を作成します。この名前でよいですか？
```

### 0-2. 同名フォルダの確認

`projects/<project>/` が既に存在する場合は中断し、次を提示する。

1. **別名で新規作成** — 新しいスラッグを提案
2. **既存プロジェクトに新機能を追加** — feature 名を別途提案・確認（後述）
3. **中止**

### 0-3. fast-track の確認

人間が「小さい」「シンプル」「fast-track」と言った場合、またはヒアリング 3〜5 問で足りる規模と判断した場合:

- `.pipeline-state.md` の `fast-track` を `有` に設定する
- 基本設計は [create-basic_design](../../abilities/create-basic_design/assets/basic-design-template.md) の簡略版（画面一覧・モジュール・データ概要のみ）でよい
- 詳細設計は **1 ファイル** に集約する（ユースケース分割しない）
- 要件定義・レビュー・人間ゲートは省略しない

### 0-4. フォルダ作成

承認後、以下を作成する。

```
projects/<project>/
├── .git/
├── .gitignore
├── .pipeline-state.md
├── docs/
│   ├── README.md
│   ├── backlog.md                  # プロジェクト横断の将来バックログ
│   ├── architecture/
│   ├── domain/
│   └── features/<feature>/
│       ├── research.md
│       ├── requirements.md
│       ├── basic-design.md
│       ├── future.md               # 本 feature の将来要件
│       ├── detailed-design/
│       ├── reviews/
│       │   ├── research.md
│       │   ├── requirements.md
│       │   ├── basic-design.md
│       │   ├── detailed-design/
│       │   ├── verify-run.md
│       │   ├── security.md         # 条件付き
│       │   ├── code.md
│       │   └── acceptance.md
└── src/
    ├── frontend/
    ├── backend/
    └── infra/
```

- `docs/README.md` は [assets/project-readme-template.md](assets/project-readme-template.md) をベースに作成する
- `.pipeline-state.md` は [assets/pipeline-state-template.md](assets/pipeline-state-template.md) をベースに作成する
- `docs/backlog.md` は [assets/backlog-template.md](assets/backlog-template.md) をベースに作成する（新規 project 時）
- `future.md` は Phase 6 で初めて内容を書く（空ファイルは作らない）

### 0-5. git 初期化

フォルダ作成後、**project ルートで** git を初期化する。初回コミットは `main` に行う。

```bash
cd projects/<project>
git init -b main
git add -A
git commit -m "chore: initialize project <project>"
```

- 既存 project への feature 追加時は初期化しない
- `.gitignore` の最低限の内容は従来どおり（Python/uv、secrets、build）

### 0-6. ブランチ作成

初回コミット後、`develop` → `feature/<feature>` を作成する（従来どおり）。

- **マージは人間が行う**。pm-agent はマージ・PR・push をしない

### コミット方針

**人間が「次へ」と承認するたびに、その段階の成果物を作業ブランチに 1 コミットする。**

| Phase | コミット例 |
|-------|-----------|
| 0 初期化 | `chore: initialize project <project>`（`main`） |
| 1 イントーク | `docs: add research and intake notes for <feature>` |
| 2 要件定義 | `docs: add requirements for <feature>` |
| 3 基本設計 | `docs: add basic design for <feature>` |
| 4 詳細設計 | `docs: add detailed design for <feature>` |
| 5 実装 | `feat: implement <feature>` |
| 6 受入 | `docs: add acceptance report for <feature>` |

差し戻し時は追加コミット（amend しない）。`.pipeline-state.md` は当該段階のコミットに含める。

**feature 名のルール**

| 状況 | feature 名 |
|------|-----------|
| 新規 project 初回 | project 名と同じ |
| 既存 project に追加 | 内容からスラッグを提案し人間に確認 |

**feature 分割の判断**

要望が複数の独立した機能群を含む場合は、feature 分割か 1 feature 内ユースケース分割かを人間に確認する。

**既存 architecture がある project に feature 追加時**

- `docs/architecture/` が既に存在する場合、create-basic_design は**既存ファイルとの整合**を必須入力とする
- 技術スタックの矛盾は Critical として review-basic_design で検出する

---

## Phase 1: イントーク

### 1-0. 事前調査（research-pre-requirements）

Phase 0 直後、イントークの前に [research-pre-requirements](../../abilities/research-pre-requirements/SKILL.md) を委譲する。

- 人間が「調査不要」「スキップ」と明示した場合のみ省略（`.pipeline-state.md` で `スキップ`）
- 省略しない場合、続けて [review-research](../../abilities/review-research/SKILL.md) を委譲する
- 自動差し戻しは **最大 1 回**（調査コストが高いため）
- Critical 解消後、調査完了報告を人間に提示して 1-1 へ

### 1-1. イントーク

案件の複雑さを判断する。

**シンプル → ヒアリング（3〜5 問）**

- `research.md` の「ヒアリングで確認すべき論点」を優先
- 回答を `docs/README.md` に追記

**複雑・曖昧 → grill-me**

[grill-me](../../coaches/grill-me/SKILL.md) を読み込み、`research.md` のパスを渡して実行する。

- 技術選定未確定、複数ステークホルダー、既存統合不明、トレードオフが必要な場合

合意後 `docs/README.md` に要約を書く。

**UI の有無（デザインシステムは選ばない）**

- フロント画面を含むかだけ確認し、README と `.pipeline-state.md` に記録する
- デザインシステムの**選択**は Phase 3 基本設計承認後に行う（画面スコープが見えてから）

### 1-2. イントーク完了ゲート

イントーク終了時、人間に提示する。

```markdown
## イントーク 完了

### 成果物
- `docs/features/<feature>/research.md`（調査実施時）
- `docs/README.md`（更新済み）

### 次のアクション
「次へ」で要件定義に進みます。「修正: ○○」でイントークを続けます。
```

| 人間の指示 | pm-agent の動作 |
|-----------|----------------|
| 「次へ」「OK」 | `docs: add research and intake notes for <feature>` をコミット → Phase 2 |
| 「修正: ○○」 | 1-1 を継続 |

---

## Phase 2: 要件定義

[create-requirement_definition](../../abilities/create-requirement_definition/SKILL.md) → [review-requirement_definition](../../abilities/review-requirement_definition/SKILL.md)

- 自動差し戻し最大 2 回（従来どおり）
- 人間承認後 `docs: add requirements for <feature>` をコミット → Phase 3

---

## Phase 3: 基本設計

[create-basic_design](../../abilities/create-basic_design/SKILL.md) → [review-basic_design](../../abilities/review-basic_design/SKILL.md)

- fast-track 時は簡略版で可
- 人間承認後 `docs: add basic design for <feature>` をコミット

### 3-1. デザインシステムの選択（UI ありの場合）

基本設計承認後、画面一覧が確定した段階で実施する。

1. `.agents/resources/design-systems/` から候補を 3〜5 個提示
2. 人間が 1 つ選ぶ（おまかせ可）
3. `docs/README.md` と `.pipeline-state.md` に記録
4. 選択結果は基本設計コミットへの**追加コミット** `docs: record design system choice` で記録（README / state のみ）

→ Phase 4 へ

---

## Phase 4: 詳細設計

[create-detailed_design](../../abilities/create-detailed_design/SKILL.md) → [review-detailed_design](../../abilities/review-detailed_design/SKILL.md)

- fast-track 時は 1 ファイルに集約
- 複数ファイル時はファイルごとに review（従来どおり）
- 人間承認後コミット → Phase 5

---

## Phase 5: 実装

### 5-1. worker 割当

詳細設計を読み、必要な worker を人間に提示して確認する。

| worker | 担当パス |
|--------|---------|
| frontend-worker | `src/frontend/` |
| backend-worker | `src/backend/` |
| devops-worker | `src/infra/` |

frontend-worker 割当時は、選択済みデザインシステムを渡す。未選択なら 3-1 を先に完了させる。

### 5-2. 実装

[implement-from-design](../../abilities/implement-from-design/SKILL.md) + worker Skills

- 必須 FR に対応するテストを実装する（AC 紐づけ）

### 5-3. verify-run

[verify-run](../../abilities/verify-run/SKILL.md) を委譲する。

- テスト実行・ビルド・起動確認
- 不合格なら implement-from-design へ差し戻し（自動最大 2 回）

### 5-4. review-code

[review-code](../../abilities/review-code/SKILL.md) を委譲する。

- 設計整合・コード品質・認可を検査（AC 最終判定は Phase 6）
- 自動差し戻し最大 2 回

### 5-5. review-security（条件付き）

次のいずれかに該当する場合、[review-security](../../abilities/review-security/SKILL.md) を委譲する。

- 認証・認可がある
- 個人情報・機密データを扱う
- 外部 API 連携がある
- 人間が明示的に要求

該当しない場合はスキップし、`.pipeline-state.md` のメモに「セキュリティレビュー: スキップ（理由）」と記す。

### 5-6. 人間ゲート

成果物 + レビュー結果を提示。承認後 `feat: implement <feature>` をコミット → Phase 6

---

## Phase 6: 最終受入

[assets/acceptance-template.md](assets/acceptance-template.md) に沿って `reviews/acceptance.md` を作成する。

1. 必須 AC の達成状況を突合する
2. 必須 FR のトレーサビリティ（要件 → 設計 → コード → テスト）を確認する
3. 権限マトリクスを検証する
4. 優先度「将来」の FR を `future.md` に移管し、`docs/backlog.md` にもリンクする
5. 未達があれば [差し戻し連鎖](#差し戻し連鎖ルール) に従い該当 Phase へ戻す
6. すべて満たせば `docs: add acceptance report for <feature>` をコミットして完了報告

完了報告では、作業ブランチ名と「`develop` へのマージは人間が行う」ことを伝える。

---

## 段階の実行パターン（Phase 2〜5 共通）

```
┌─ 自動（人間を待たない）──────────────┐
│  1. create-<stage> を実行            │
│  2. review-<stage> を直ちに実行      │
│  3. Critical があれば差し戻し        │
└─────────────────────────────────────┘
        ↓
  4. 成果物 + レビュー結果を人間に提示
        ↓
  5. 人間の指示を待つ
```

Phase 5 は create の代わりに implement → verify-run → review-code（→ review-security）の順。

### Critical の自動差し戻し

- **上限:** 事前調査 1 回、その他 2 回
- 上限後も Critical 残存時はその旨を添えて人間に提示
- 差し戻し発生時 `.pipeline-state.md` のメトリクスを更新する

### 人間への提示フォーマット

```markdown
## [段階名] 完了

### 成果物
- パス: `projects/<project>/docs/features/<feature>/...`

### レビュー結果
- Critical: なし / あり（内容）
- Suggestion: （あれば列挙）

### 次のアクション
「次へ」で次段階に進みます。「修正: ○○」で差し戻します。
```

### 人間ゲート

| 人間の指示 | pm-agent の動作 |
|-----------|----------------|
| 「次へ」「OK」「進めて」 | 当該段階を git commit → 次 Phase |
| 「修正: ○○」 | 差し戻し（下記） |
| worker 構成の変更 | 割当を更新して再実行 |

---

## 差し戻し連鎖ルール

人間またはレビューで前段階の成果物を変更する必要が生じたとき、影響範囲を判定する。

| 変更起点 | 通常の影響先 |
|----------|-------------|
| イントーク / README | 要件定義 → 基本設計 → 詳細設計 → 実装 |
| 要件定義 | 基本設計 → 詳細設計 → 実装 |
| 基本設計 | 詳細設計 → 実装 |
| 詳細設計 | 実装のみ |
| 実装 | 実装のみ |

手順:

1. `.pipeline-state.md` の「巻き戻し起点」「影響先」「理由」を更新する
2. メトリクスの「人間差し戻し回数」を +1 する
3. 影響先の最古 Phase から順に create → review を再実行する
4. すでに承認済みの downstream 段階は「差し戻し中」に戻す

fast-track でも連鎖ルールは同じ。省略されるのは文書の厚さのみ。

---

## Skill 委譲マップ

```
pm-agent
├── coaches/
│   └── grill-me
├── abilities/
│   ├── research-pre-requirements
│   ├── review-research
│   ├── create-requirement_definition
│   ├── review-requirement_definition
│   ├── create-basic_design
│   ├── review-basic_design
│   ├── create-detailed_design
│   ├── review-detailed_design
│   ├── implement-from-design
│   ├── verify-run
│   ├── review-code
│   ├── review-security          # 条件付き
│   └── (Phase 6 は pm-agent が acceptance.md を作成)
└── workers/
    ├── frontend-worker
    ├── backend-worker
    └── devops-worker
```

各段階では対応 Skill を **必ず読み込んでから** 実行する。

---

## 状態の追跡

進行状況は `projects/<project>/.pipeline-state.md` に記録する。

- 各段階の承認・差し戻し・worker 割当のたびに更新する
- 承認日時・メトリクスを記録する
- 会話内でも project / feature・Phase・待ち状態を明示する

### セッション再開

`.pipeline-state.md` を読み、現在の Phase と次のアクションを人間に伝えてから続行する。
