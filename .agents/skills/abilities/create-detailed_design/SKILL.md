---
name: create-detailed_design
description: >-
  Creates a detailed design document (詳細設計書) from approved basic design.
  Specifies APIs, DB schema, error handling, and sequences. Writes one or
  more files under detailed-design/ in Japanese. Use when pm-agent delegates the detailed
  design stage, or when creating or revising a 詳細設計書.
---

# 詳細設計書の作成

承認済み基本設計をもとに、実装に必要な仕様を具体化する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| API・DB・画面・エラー・シーケンスの具体化 | ソースコードの作成 |
| エンドポイント単位の認可定義 | インフラのプロビジョニング |
| 要件・基本設計とのトレーサビリティ | 基本設計の方針変更 |

実装は次段階（implement-from-design + worker）に委ねる。

---

## 入力

| 入力 | パス |
|------|------|
| 要件定義書 | `docs/features/<feature>/requirements.md` |
| 基本設計書 | `docs/features/<feature>/basic-design.md` |
| 技術スタック | `docs/architecture/tech-stack.md` |
| システム境界 | `docs/architecture/system-context.md` |
| 前回版（差し戻し時） | `docs/features/<feature>/detailed-design/*.md` |
| 修正指示 | レビュー指摘 or 人間からのフィードバック |

基本設計の未決事項を可能な限り解消する。技術スタックに反する選定はしない。

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/detailed-design/<usecase>.md`

基本設計のモジュール構成に応じて、ユースケース単位で**1つ以上**のファイルを作成する（例: `stock-update.md`, `stock-alert.md`）。詳細設計が1つだけでも `detailed-design/` フォルダに格納する。

**言語:** 日本語（API パス・識別子・型名は英語）

各ファイルは [assets/detailed-design-template.md](assets/detailed-design-template.md) に従う。

---

## 執筆手順

1. 要件定義・基本設計・architecture を読み、FR と認可方針を把握する
2. 基本設計のモジュール一覧（§2.1）・機能フローから、詳細設計の**分割単位（ユースケース）**を決める
3. ユースケースごとに `detailed-design/<usecase>.md` を作成する
4. 各ファイルの冒頭に、担当するユースケース / モジュールと要件 ID を明記する
5. 全 API に必要ロールを付与する（要件の権限マトリクスと一致させる）
6. 全必須 FR が、いずれかのファイルの API / DB / 画面に対応していることを確認する
7. 未決事項を整理する（実装へ回すものは明示）

### 分割の指針

- **機能（ユースケース）単位**で分ける（例: 在庫更新、アラート）
- **レイヤ（frontend / backend）はファイル分割しない** — 各ファイル内の章（API 設計・画面詳細）で扱う
- 小さい feature では1ファイルでよい（それでも `detailed-design/` に置く）
- ファイル名はユースケースの英小文字スラッグ（例: `stock-update.md`）

### 差し戻し時

- 指摘対象のファイルを特定し、そのファイルの**全体版**を書き直す
- ユースケースの追加・分割が必要な場合はファイルを新設する

---

## 記述ガイドライン

### API

- RESTful を基本（architecture の方針に従う）
- パスは `/api/v1/` プレフィックスを推奨
- リクエスト / レスポンスは JSON 例を記載する
- 各エンドポイントに要件 ID と必要ロールを付ける

### データベース

- テーブル名・カラム名は英語（snake_case）
- 型・NULL・制約・インデックスを明記する
- 基本設計のエンティティと対応づける

### 認可

- 要件定義の権限マトリクスと矛盾しないこと
- 403 のエラーコードと発生条件を定義する
- 認可チェックのタイミング（ミドルウェア / ハンドラ）を §7 に記載する

### 画面

- UI がない場合は「該当なし」と明記する
- 項目ごとにバリデーションと呼び出し API を対応づける

### シーケンス

- 主要ユースケースを 1 本以上、Mermaid で記述する
- 認可エラー時のフローも必要に応じて含める

---

## 品質チェック（自己確認）

- [ ] 全必須 FR が、いずれかの詳細設計ファイルに対応している
- [ ] 各ファイルの担当範囲（ユースケース / モジュール / FR）が明記されている
- [ ] 全 API に認可（必要ロール）が定義されている
- [ ] 要件・基本設計の権限と矛盾していない
- [ ] DB 定義が API と整合している（複数ファイル間でも矛盾がない）
- [ ] エラーコードが主要失敗パスをカバーしている
- [ ] 日本語説明 + 英語識別子で統一されている

---

## 次の段階への引き継ぎ

[review-detailed_design](../review-detailed_design/SKILL.md) でレビューされる。

人間承認後、pm-agent が [implement-from-design](../implement-from-design/SKILL.md) + worker に委譲する。
