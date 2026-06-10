# バックエンド ディレクトリ構成

`architecture/tech-stack.md` のバックエンド技術に合わせて調整する。未指定時は Python + FastAPI を前提とする。

```
src/backend/
├── pyproject.toml               # uv で管理
├── uv.lock                      # uv のロックファイル（コミットする）
├── alembic/                     # DB マイグレーション（該当時）
│   └── versions/
├── app/
│   ├── main.py                  # エントリポイント
│   ├── api/
│   │   ├── routes/              # エンドポイント（詳細設計 §2）
│   │   └── deps.py              # 依存注入（DB セッション等）
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py          # 認証・認可
│   │   └── errors.py            # エラーコード（詳細設計 §5）
│   ├── models/                  # DB モデル（詳細設計 §3）
│   ├── schemas/                 # リクエスト / レスポンス型
│   ├── services/                # ビジネスロジック
│   └── repositories/            # データアクセス（必要時）
└── tests/
```

## 命名規則

- モジュール・関数: snake_case
- クラス: PascalCase
- テーブル名: snake_case 複数形（`inventories`）
- API パス: 詳細設計どおり（`/api/v1/...`）
- 識別子は英語

## レイヤの責務

| レイヤ | 責務 |
|--------|------|
| routes | HTTP 入出力、認可デコレータ、バリデーション呼び出し |
| services | ビジネスロジック |
| models / repositories | 永続化 |
| core/security | ロールチェック、403 返却 |
