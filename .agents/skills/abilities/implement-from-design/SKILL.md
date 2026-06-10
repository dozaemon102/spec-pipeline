---
name: implement-from-design
description: >-
  Implements code from approved detailed design documents. Reads
  detailed-design/*.md and delegates layer-specific work to assigned workers.
  Writes to projects/<project>/src/. Use when pm-agent delegates the code
  stage, or when implementing from a 詳細設計書.
---

# 詳細設計からの実装

承認済み詳細設計書に基づき、割当られた worker と協力してコードを作成する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 詳細設計に基づくコード実装 | 設計書の変更 |
| worker への作業分担と統合 | 設計にない機能の追加 |
| 認可・エラーハンドリングの実装 | レビュー（review-code に委譲） |

フレームワーク固有の規約は各 worker Skill に従う。

---

## 入力

| 入力 | パス |
|------|------|
| 詳細設計書 | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 要件定義書（参照） | `projects/<project>/docs/features/<feature>/requirements.md` |
| 技術スタック | `projects/<project>/docs/architecture/tech-stack.md` |
| 割当 worker | pm-agent から指定（例: backend-worker, frontend-worker） |
| 修正指示 | レビュー指摘 or 人間からのフィードバック |

---

## 出力

| 担当 | パス |
|------|------|
| フロントエンド | `projects/<project>/src/frontend/` |
| バックエンド | `projects/<project>/src/backend/` |
| インフラ | `projects/<project>/src/infra/` |

**言語:** コード・API 識別子は英語。コメントは必要最小限（英語推奨）。

---

## 実装手順

1. `detailed-design/*.md` をすべて読み、API / DB / 画面 / 認可を把握する
2. pm-agent から割当られた worker Skill を読み込む
3. 実装順序を決める（下記）
4. ユースケース（詳細設計ファイル）ごとに、割当 worker が担当レイヤを実装する
5. [assets/implementation-checklist.md](assets/implementation-checklist.md) で自己確認する

### 実装順序（推奨）

```
1. backend-worker  → API / DB / 認可（詳細設計の依存関係に従う）
2. frontend-worker → 画面・クライアント（API 実装後）
3. devops-worker   → インフラ設定（必要な場合、最後）
```

複数の `detailed-design/<usecase>.md` がある場合:

- ファイルごとに backend → frontend の順で実装する
- `_cross-cutting.md`（レビュー結果）に依存関係があればそれに従う
- 共有 DB テーブルは最初のユースケースで定義し、以降は拡張のみ

### worker との協働

| worker | 読み込み | 担当 |
|--------|---------|------|
| [backend-worker](../../workers/backend-worker/SKILL.md) | 必須（割当時） | `src/backend/` |
| [frontend-worker](../../workers/frontend-worker/SKILL.md) | 必須（割当時） | `src/frontend/` |
| [devops-worker](../../workers/devops-worker/SKILL.md) | 必須（割当時） | `src/infra/` |

- 本 Skill は**何を実装するか**（詳細設計の内容）を定義する
- 各 worker は**どう実装するか**（フレームワーク・ディレクトリ構成・規約）を定義する
- 両方の Skill が矛盾する場合は、詳細設計の仕様を優先し、worker の規約で表現する

### 認可の実装

- 詳細設計 §2（API）の「必要ロール」列に従う
- 403 エラーは詳細設計 §5 のエラーコード定義に合わせる
- 要件定義の権限マトリクスと矛盾しないことを確認する

### 差し戻し時

- 指摘対象のファイル・エンドポイント・画面を特定して修正する
- 設計と実装が乖離している場合は、**設計を変えずに実装を直す**（設計変更が必要なら人間にエスカレーション）

---

## 設計との対応づけ

実装時、詳細設計の要素をコードに対応づける。

| 詳細設計 | 実装 |
|----------|------|
| §2 API | ルート / ハンドラ / コントローラ |
| §3 DB | マイグレーション / モデル / リポジトリ |
| §4 画面 | コンポーネント / ページ / フォーム |
| §5 エラー | 例外クラス / エラーレスポンス |
| §7 認可 | ミドルウェア / デコレータ / ガード |

---

## 品質チェック（自己確認）

[assets/implementation-checklist.md](assets/implementation-checklist.md) の全項目を確認する。

---

## 次の段階への引き継ぎ

[review-code](../review-code/SKILL.md) でレビューされる。

人間承認後、pm-agent が完了報告する。
