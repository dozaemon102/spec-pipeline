# フロントエンド ディレクトリ構成

`architecture/tech-stack.md` のフロントエンド技術に合わせて調整する。未指定時は React + TypeScript を前提とする。

```
src/frontend/
├── package.json
├── tsconfig.json
├── src/
│   ├── main.tsx                 # エントリポイント
│   ├── app/                     # ルーティング・プロバイダ
│   ├── pages/                   # 画面（詳細設計 §4 に対応）
│   ├── components/              # 再利用 UI
│   ├── api/                     # API クライアント（詳細設計 §2 に対応）
│   ├── hooks/                   # カスタムフック
│   ├── types/                   # 型定義（API レスポンス等）
│   ├── auth/                    # 認可コンテキスト・ガード
│   └── utils/
└── public/
```

## 命名規則

- ファイル・コンポーネント: PascalCase（`InventoryList.tsx`）
- フック: `use` プレフィックス（`useInventory.ts`）
- API 関数: 動詞 + リソース（`fetchInventory`, `updateStock`）
- 識別子は英語

## 画面と詳細設計の対応

| 詳細設計 | 実装 |
|----------|------|
| §4 画面詳細 | `pages/` + `components/` |
| §2 API | `api/` のクライアント関数 |
| 必要ロール | `auth/` で UI 制御（非表示 / 無効化） |
