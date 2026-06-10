---
name: devops-worker
description: >-
  Implements infrastructure and deployment config under src/infra/.
  Docker, CI/CD, and environment setup per tech-stack. Use when pm-agent
  assigns devops work, or during implement-from-design for infra layers.
---

# DevOps Worker

`src/infra/` 配下のインフラ・デプロイ設定を担当する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| Docker / CI/CD / デプロイ設定 | アプリケーションコードの実装 |
| 環境変数テンプレート（`.env.example`） | シークレットのコミット |
| `src/infra/` 内の設定 | 詳細設計の変更 |

[implement-from-design](../../abilities/implement-from-design/SKILL.md) の手順に従う。backend / frontend の実装後に実行する。

---

## 入力

| 入力 | パス |
|------|------|
| 技術スタック | `docs/architecture/tech-stack.md` |
| 詳細設計書（参照） | `docs/features/<feature>/detailed-design/*.md` |
| 要件定義書（参照） | `docs/features/<feature>/requirements.md`（NFR） |
| 修正指示 | review-code or 人間からのフィードバック |

---

## 出力

**パス:** `projects/<project>/src/infra/`

---

## 実装手順

1. `tech-stack.md` でインフラ・CI/CD 技術を確認する
2. [assets/directory-structure.md](assets/directory-structure.md) に従い構成を整える
3. フロント / バックの Dockerfile を作成する
4. ローカル開発用 `docker-compose.yml` を作成する
5. NFR（可用性・性能）に応じた設定を反映する
6. `.env.example` に必要な環境変数を列挙する

### セキュリティ

- パスワード・API キー・トークンをリポジトリに含めない
- 本番用シークレットは環境変数 or シークレット管理サービスで注入する
- 最小権限の原則で IAM / ネットワークを設定する

### tech-stack 未記載時

- Docker + docker-compose をデフォルトとする
- CI は GitHub Actions をデフォルトとする
- 人間の指示または tech-stack 更新があればそれに従う

---

## 品質チェック

- [ ] `docker-compose up` でローカル起動できる（または手順が明記されている）
- [ ] シークレットがリポジトリに含まれていない
- [ ] 環境変数が `.env.example` に文書化されている
- [ ] コードが `src/infra/` 内に収まっている
