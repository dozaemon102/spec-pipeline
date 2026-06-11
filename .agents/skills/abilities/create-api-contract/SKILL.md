---
name: create-api-contract
description: >-
  Creates OpenAPI contract and shared types from approved detailed design,
  before implementation. Writes to contract/openapi.yaml. Use when pm-agent
  delegates API contract stage after detailed design, or before implementation.
---

# API 契約の作成

詳細設計の API 定義から、実装前の契約（OpenAPI）を生成する。コードは書かない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| OpenAPI 3.x の生成 | 実装コードの作成 |
| 詳細設計との対応表 | 詳細設計の変更 |
| 共有型定義（参照用） | サーバー・クライアントの実装 |

backend 未割当（API なし）の場合は「該当なし」と記録してスキップ可能。

---

## 入力

| 入力 | パス |
|------|------|
| 詳細設計書 | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 基本設計書 | `projects/<project>/docs/features/<feature>/basic-design.md` |
| 技術スタック | `projects/<project>/docs/architecture/tech-stack.md` |
| 修正指示 | review-api-contract or 人間からのフィードバック |

---

## 出力

| 成果物 | パス |
|--------|------|
| OpenAPI | `projects/<project>/docs/features/<feature>/contract/openapi.yaml` |
| 型定義（frontend 用・任意） | `projects/<project>/docs/features/<feature>/contract/types.ts` |

`contract/` が未作成なら作成する。

OpenAPI は詳細設計 §2 の全エンドポイントを含む。パス・メソッド・リクエスト/レスポンス・認可要件を反映する。

---

## 作成手順

1. `detailed-design/*.md` の API 定義をすべて読み込む
2. OpenAPI 3.0 以上で `openapi.yaml` を作成する
3. frontend 割当がある場合、`types.ts` に主要リクエスト/レスポンス型を export する（参照用スタブ）
4. 詳細設計ファイルごとのエンドポイント対応をコメントまたは `x-usecase` 拡張で紐づける

### 差し戻し時

- 指摘を反映した全体版を書き直す

---

## 品質チェック（自己確認）

- [ ] 詳細設計の全 API が openapi.yaml に含まれている
- [ ] 設計にないエンドポイントを追加していない
- [ ] 認可要件（必要ロール）が description または security で記載されている

---

## 次の段階への引き継ぎ

[review-api-contract](../review-api-contract/SKILL.md) でレビューされる。

人間承認後、pm-agent が Phase 5（実装）へ進む。
