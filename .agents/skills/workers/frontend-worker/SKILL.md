---
name: frontend-worker
description: >-
  Implements frontend code under src/frontend/ from detailed design.
  Follows tech-stack and UI conventions. Use when pm-agent assigns
  frontend work, or during implement-from-design for UI layers.
---

# Frontend Worker

`src/frontend/` 配下の UI 実装を担当する。詳細設計 §4（画面）と §2（API 呼び出し）を実装する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| 画面・コンポーネント・API クライアント | バックエンド API の実装 |
| 認可に基づく UI 制御 | 詳細設計の変更 |
| `src/frontend/` 内のコード | `src/backend/` への書き込み |

[implement-from-design](../../abilities/implement-from-design/SKILL.md) の手順に従い、本 Skill は実装方法を定義する。

---

## 入力

| 入力 | パス |
|------|------|
| 詳細設計書 | `docs/features/<feature>/detailed-design/*.md` |
| 技術スタック | `docs/architecture/tech-stack.md` |
| デザインシステム（選択時） | `.agents/resources/design-systems/<name>/DESIGN.md` |
| 修正指示 | review-code or 人間からのフィードバック |

---

## 出力

**パス:** `projects/<project>/src/frontend/`

**言語:** TypeScript / JavaScript（tech-stack に従う）。識別子は英語。

---

## 実装手順

1. `tech-stack.md` でフロントエンド技術を確認する
2. [assets/directory-structure.md](assets/directory-structure.md) に従い構成を整える
3. 詳細設計 §4 の画面を `pages/` に実装する
4. 詳細設計 §2 の API を `api/` にクライアントとして実装する
5. 詳細設計の「必要ロール」に基づき UI を制御する

### 認可（UI）

- ロール不足の操作はボタン非表示または無効化する
- サーバー側認可の代替にならない（UI 制御は補助）
- 403 レスポンスはユーザーに分かる形で表示する

### バリデーション

- 詳細設計 §4 のバリデーションをフォームに実装する
- クライアント側バリデーションに加え、サーバーエラーを表示する

### デザインシステム（選択時）

pm-agent が案件ごとにデザインシステムを選んでいる場合（`.pipeline-state.md` の「デザインシステム」欄に記載）、以下に従う。

1. `.agents/resources/design-systems/<name>/DESIGN.md` を読む
2. frontmatter の `colors` / `typography` / `rounded` / `spacing` を **CSS 変数（`:root`）** に落とす
3. `components`（button / card / input 等）のトークン値を各 UI コンポーネントに適用する
4. proprietary フォントは本文「Note on Font Substitutes」の無償代替（Inter / Geist / JetBrains Mono 等）を使う
5. DESIGN.md の「Do's and Don'ts」を逸脱しない（例: ダーク専用システムでライトモードを作らない）

- 選択がない場合は、詳細設計 §4 と技術スタックに従い素朴で読みやすい UI にする（独自に華美な装飾を足さない）
- デザインシステムは **見た目** を規定する。画面項目・API 対応・バリデーションは詳細設計が優先する

### tech-stack 未記載時

- React + TypeScript + Vite をデフォルトとする
- 人間の指示または tech-stack 更新があればそれに従う

---

## 品質チェック

- [ ] 詳細設計の全画面が実装されている
- [ ] API クライアントが設計のパス・メソッドと一致している
- [ ] 識別子が英語である
- [ ] コードが `src/frontend/` 内に収まっている
