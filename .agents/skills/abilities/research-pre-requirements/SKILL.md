---
name: research-pre-requirements
description: >-
  Searches the web for domain reference information before requirements
  definition. Produces research.md to support human intake and requirements
  design sessions. Use when pm-agent delegates pre-requirements research,
  or before creating a 要件定義書.
---

# 要件定義前の事前調査

ネット上の公開情報を調査し、人間との要件設計・ヒアリングで使えるたたき台を文書化する。

## やること / やらないこと

| やること | やらないこと |
|----------|-------------|
| Web 検索・主要ソースの取得と要約 | 要件定義書の直接執筆 |
| 類似事例・ドメイン知識の整理 | 基本設計・技術選定の確定 |
| ヒアリング論点・スコープ示唆の列挙 | 人間への質問（ヒアリングは pm-agent / grill-me） |
| 出典 URL と信頼度の記録 | コード・設計書の作成 |

調査結果は**参考情報**。最終要件は人間の判断とイントークで確定する。

---

## 入力

| 入力 | パス / ソース |
|------|--------------|
| プロジェクト概要 | `projects/<project>/docs/README.md` |
| 初期要望 | 会話履歴・README の要約 |
| feature 名 | `.pipeline-state.md` の「現在の feature」 |
| 既存調査（再調査時） | `projects/<project>/docs/features/<feature>/research.md` |
| 追加指示 | 人間からの「○○を重点的に調べて」等 |

入力が薄い場合は README の要約だけから調査を開始する。止まって質問しない。

---

## 出力

**パス:** `projects/<project>/docs/features/<feature>/research.md`

**言語:** 日本語（参考リンクのタイトルは原文可）

[assets/research-template.md](assets/research-template.md) をベースに作成する。

---

## 調査手順

### 1. 調査計画

README と要望から、次を整理する。

- **ドメイン**（業界・課題・利用者）
- **類似物**（競合 SaaS、OSS、社内類似システムの有無）
- **制約候補**（規制、認証、データ所在地）
- **不明点**（ネットで補えるか / 人間に聞くべきか）

### 2. Web 検索

`WebSearch` を **3〜6 回**、角度を変えて実行する。例:

| 角度 | クエリ例 |
|------|----------|
| 類似製品 | `<ドメイン> SaaS 機能 比較` |
| OSS | `<ドメイン> open source github` |
| ベストプラクティス | `<ドメイン> best practices requirements` |
| 規制・標準 | `<業界> 法規制 システム 要件`（該当時） |
| 失敗・課題 | `<ドメイン> 導入 失敗 課題` |
| 日本市場 | 日本語クエリで国内事例・用語 |

英語ドメインは英語クエリも併用する。同一クエリの繰り返しは避ける。

### 3. 深掘り

検索結果のうち **3〜8 件** を `WebFetch` で読む。

優先順位:

1. 公式ドキュメント・規制原文
2. 信頼できる技術記事・事例
3. OSS の README / 機能一覧
4. レビューサイト・ブログ（二次情報として要約）

取得できない URL は「未取得」と記し、検索スニペットのみで扱う。

### 4. コードベース（該当時）

既存プロジェクトへの feature 追加、または README に社内システム言及がある場合は、リポジトリ内も検索する。社内仕様がネットに無い場合は「社内確認が必要」と明記する。

### 5. 文書化

テンプレートに沿って `research.md` を書く。

- **セクション 9（ヒアリング論点）** は必須。調査で埋められなかったギャップを質問形式で列挙する
- **セクション 10（スコープ示唆）** は「含める候補」「除外候補」「判断が分かれる点」に分ける
- 断定できない情報は「〜の可能性」「要確認」とし、出典を付ける
- 設計・実装の具体（API、DB、フレームワーク選定）は書かない

### 6. 自己確認

保存前に確認する。

- [ ] 主要な検索角度を少なくとも 3 つカバーした
- [ ] 参考リンクに URL と要約がある
- [ ] ヒアリング論点が 5 件以上（薄いドメインは 3 件以上）
- [ ] スコープ示唆が要件レベル（実装詳細でない）
- [ ] 未調査・情報不足が正直に書かれている
- [ ] 日本語で読める

---

## pm-agent への返却

調査完了後、次を会話で短く報告する（人間向け）。

```markdown
## 事前調査 完了

### 成果物
- `projects/<project>/docs/features/<feature>/research.md`

### 要点（3〜5 行）
- ...

### ヒアリングで優先確認したいこと（上位 3 件）
1. ...
2. ...
3. ...

### 次のアクション
イントーク（ヒアリング / grill-me）に進みます。
```

---

## 次の段階への引き継ぎ

- **イントーク:** セクション 9・10 を論点のたたき台に使う
- **要件定義:** [create-requirement_definition](../create-requirement_definition/SKILL.md) の入力として `research.md` を読む
- **再調査:** イントークでドメインが大きく変わった場合、pm-agent が本 Skill を再実行する
