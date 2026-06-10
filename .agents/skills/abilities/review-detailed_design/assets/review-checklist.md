# 詳細設計書レビュー チェックリスト

`detailed-design/` 内の**全ファイル**を読み込み、ファイル単位と横断の両方で確認する。

**レビュー対象**

- `docs/features/<feature>/detailed-design/*.md`（1つ以上）
- （参照）`docs/features/<feature>/requirements.md`
- （参照）`docs/features/<feature>/basic-design.md`
- （参照）`docs/architecture/tech-stack.md`

---

## A. ファイル単位（各 `<usecase>.md` ごと）

| ID | 区分 | 確認項目 | 判定基準 |
|----|------|----------|----------|
| F-C01 | Critical | 担当範囲の明記 | ユースケース / モジュール / 担当 FR が冒頭にない |
| F-C02 | Critical | API の認可 | エンドポイントに必要ロールが未定義 |
| F-C03 | Critical | 権限の不整合 | 要件の権限マトリクスと API のロールが矛盾 |
| F-C04 | Critical | DB と API | 同一ファイル内で API が参照するデータが DB 定義にない |
| F-C05 | Critical | 基本設計との矛盾 | 基本設計のモジュール・認可方針と矛盾 |
| F-S01 | Suggestion | リクエスト / レスポンス | JSON 例がないエンドポイントがある |
| F-S02 | Suggestion | エラーコード | 403 以外の主要エラーが未定義 |
| F-S03 | Suggestion | シーケンス | 主要フローの Mermaid がない |
| F-S04 | Suggestion | 画面詳細 | UI があるのに画面詳細がない（または「該当なし」明記がない） |
| F-S05 | Suggestion | トレーサビリティ | 担当 FR と設計要素の対応表がない |

---

## B. 横断（`detailed-design/` 全体）

| ID | 区分 | 確認項目 | 判定基準 |
|----|------|----------|----------|
| X-C01 | Critical | FR の漏れ | 必須 FR がどのファイルにも対応していない |
| X-C02 | Critical | API の重複 | 同一メソッド + パスが複数ファイルで定義されている |
| X-C03 | Critical | DB の矛盾 | 同一テーブルがファイル間で定義が矛盾している |
| X-C04 | Critical | 認可の抜け | 要件にロール制限がある操作に対応する API がない |
| X-S01 | Suggestion | 分割の妥当性 | 無関係なユースケースが1ファイルに混在している |
| X-S02 | Suggestion | ファイル間参照 | ユースケース間の依存が文書化されていない |
| X-S03 | Suggestion | 未決事項 | 基本設計の OPN が引き継がれていない |

---

## 判定ルール

**ファイル単位**

- ファイルに F-Critical が1件でもあれば、そのファイルは **不合格**
- F-Suggestion のみなら **条件付き合格**

**段階全体（pm-agent へ返す判定）**

- いずれかのファイルまたは横断チェックに Critical が1件でもあれば **不合格**
- Critical なし・Suggestion のみなら **条件付き合格**
- 指摘なしなら **合格**
