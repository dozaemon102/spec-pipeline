---
name: review-detailed_design
description: >-
  Reviews detailed design documents (詳細設計書) for API, DB, auth, and
  cross-file consistency. Supports multiple files under detailed-design/.
  Outputs per-file and cross-cutting review results. Use when pm-agent
  delegates detailed design review, or when reviewing a 詳細設計書.
---

# 詳細設計書のレビュー

`detailed-design/` 内の全ファイルを検査し、要件・基本設計との整合性とファイル間の一貫性を確認する。文書の修正は行わない。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| ファイル単位・横断の品質検査 | 詳細設計書の直接修正 |
| FR カバレッジの確認 | コードの妥当性判断 |
| Critical / Suggestion の分類 | 人間ゲートの判断 |

---

## 入力

| 入力 | パス |
|------|------|
| 詳細設計書（1つ以上） | `projects/<project>/docs/features/<feature>/detailed-design/*.md` |
| 要件定義書（参照） | `projects/<project>/docs/features/<feature>/requirements.md` |
| 基本設計書（参照） | `projects/<project>/docs/features/<feature>/basic-design.md` |
| 技術スタック（参照） | `projects/<project>/docs/architecture/tech-stack.md` |

---

## 出力

| 成果物 | パス | テンプレート |
|--------|------|-------------|
| ファイルごとのレビュー | `reviews/detailed-design/<usecase>.md` | [review-output-template.md](assets/review-output-template.md) |
| 横断レビュー | `reviews/detailed-design/_cross-cutting.md` | [cross-review-output-template.md](assets/cross-review-output-template.md) |

**言語:** 日本語

`reviews/detailed-design/` が未作成の場合は作成する。

---

## レビュー手順

1. `detailed-design/*.md` の全ファイルを読み込む
2. 要件定義・基本設計と照合し、FR と権限マトリクスを把握する
3. **ファイルごと**に [assets/review-checklist.md](assets/review-checklist.md) §A を確認し、`reviews/detailed-design/<usecase>.md` を保存する
4. **横断**で §B を確認し、`reviews/detailed-design/_cross-cutting.md` を保存する
5. 段階全体の判定を pm-agent に返す

### 段階全体の判定

| 判定 | 条件 | pm-agent の動作 |
|------|------|----------------|
| 不合格 | いずれかのファイルまたは横断で Critical が1件以上 | 自動差し戻し（create-detailed_design へ） |
| 条件付き合格 | Critical なし・Suggestion のみ | 人間に提示 |
| 合格 | 指摘なし | 人間に提示 |

差し戻し時は、Critical のあったファイル名と横断指摘を create に渡す。

---

## 指摘の書き方

- **該当箇所:** ファイル名 + セクション（例: `stock-update.md §2.2`, `stock-alert.md §3.2`）
- **指摘:** 何が問題か（1文）
- **修正案:** create が直せる具体的内容

```
✅ stock-update.md §2.1: POST /api/v1/inventory に必要ロールが未記載。ROLE-001 を追加する
✅ 横断 X-C01: FR-003 がどのファイルにも未対応。stock-alert.md に通知 API を追加する
❌ API 設計が不十分（曖昧）
```

---

## Critical の例

| 状況 | 理由 |
|------|------|
| 必須 FR が全ファイルで未対応 | 実装の根拠がない |
| 同一 API が2ファイルで定義 | 実装時に衝突する |
| 権限マトリクスと API ロールが矛盾 | セキュリティ欠陥の原因 |
| テーブル定義がファイル間で矛盾 | データ不整合の原因 |

## Suggestion の例

| 状況 | 理由 |
|------|------|
| シーケンス図がない | 実装は可能だが、認識齟齬のリスク |
| ユースケース間の依存が未記載 | 実装順序の判断が難しい |

---

## 次の段階への引き継ぎ

- **不合格** → [create-detailed_design](../create-detailed_design/SKILL.md) が指摘を反映して再作成
- **合格 / 条件付き合格** → pm-agent が人間ゲートへ。承認後 [implement-from-design](../implement-from-design/SKILL.md) + worker へ
