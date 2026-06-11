# hotfix モード（fix/ ブランチ）

不具合修正のみを行うときの短縮パイプライン。feature 開発の Phase 0〜4 を省略する。

## 起動条件

- 作業ブランチが `fix/<topic>`
- 人間が「hotfix」「不具合修正」「バグ修正」と明示
- 既存 project で詳細設計が存在する

## フロー

```
1. 影響ユースケースの特定（どの detailed-design/*.md に該当するか）
2. 必要なら該当 detailed-design の差分更新 → review-detailed_design
3. 必要なら contract/openapi.yaml の差分更新 → review-api-contract
4. implement-from-design（該当範囲のみ）
5. verify-run → review-code → review-security（条件付き）
6. create-acceptance → review-acceptance（簡略可: 影響 FR/AC のみ）
```

## 省略できるもの

| 通常 Phase | hotfix |
|-----------|--------|
| 0 初期化 | 省略（既存 project） |
| 1 イントーク | 省略（修正内容を README メモに追記可） |
| 2 要件定義 | 省略（既存 requirements を参照） |
| 3 基本設計 | 省略 |
| 4 詳細設計 | 差分のみ or 省略（設計に該当がある場合） |
| 4.5 API 契約 | 差分のみ or 省略 |

## コミット

| 段階 | コミット例 |
|------|-----------|
| 設計差分（あれば） | `docs: fix design for <topic>` |
| 契約差分（あれば） | `docs: fix api contract for <topic>` |
| 実装 | `fix: <topic>` |
| 受入 | `docs: add acceptance report for fix/<topic>` |

## 注意

- 設計に該当ユースケースがない新規バグは、hotfix ではなく feature または詳細設計追加から始める
- スコープが広がった場合は通常パイプラインへエスカレーションする
