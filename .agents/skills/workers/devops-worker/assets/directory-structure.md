# インフラ ディレクトリ構成

`architecture/tech-stack.md` のインフラ技術に合わせて調整する。

```
src/infra/
├── docker/
│   ├── Dockerfile.backend
│   ├── Dockerfile.frontend
│   └── docker-compose.yml       # ローカル開発用
├── terraform/                   # 該当時
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── k8s/                         # 該当時
├── scripts/
│   ├── deploy.sh
│   └── migrate.sh
└── .env.example                 # シークレットは含めない
```

## 原則

- シークレット・API キーをリポジトリにコミットしない
- 環境変数名は英語・大文字スナーク（`DATABASE_URL`）
- `tech-stack.md` の CI/CD ツールに合わせてパイプライン設定を置く
- フロント / バックのビルド・起動方法を `docker-compose.yml` で統一する

## 詳細設計との対応

| 詳細設計 / architecture | 実装 |
|------------------------|------|
| tech-stack §インフラ | デプロイ先・コンテナ構成 |
| NFR（可用性・性能） | レプリカ数、ヘルスチェック |
| 外部連携 | ネットワーク・ファイアウォール設定 |
