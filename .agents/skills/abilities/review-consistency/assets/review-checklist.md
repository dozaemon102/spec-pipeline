# 段階横断整合チェックリスト

## Phase 2: research ↔ requirements

| ID | 種別 | 確認項目 |
|----|------|----------|
| C-01 | Critical | 調査の「含める候補」に挙がった主要機能が requirements のスコープ外になっている（意図的除外の記載なし） |
| C-02 | Critical | 調査の規制・コンプライアンス項目が requirements に未反映（該当なしの明記もない） |
| S-01 | Suggestion | ヒアリング論点の過半数が requirements で未決のまま |

## Phase 3: requirements ↔ basic-design + architecture

| ID | 種別 | 確認項目 |
|----|------|----------|
| C-01 | Critical | 必須 FR に対応する設計要素が basic-design にない |
| C-02 | Critical | 権限マトリクスが認証・認可方針に反映されていない |
| C-03 | Critical | 既存 architecture がある場合、tech-stack が矛盾（feature 追加時） |
| S-01 | Suggestion | NFR が tech-stack 選定理由と対応づいていない |

## Phase 4: basic-design ↔ detailed-design + requirements

| ID | 種別 | 確認項目 |
|----|------|----------|
| C-01 | Critical | basic-design のモジュール・画面が detailed-design に存在しない |
| C-02 | Critical | 必須 FR が detailed-design でカバーされていない |
| C-03 | Critical | basic-design のデータ概念と detailed-design の DB 定義が矛盾 |
| S-01 | Suggestion | basic-design の未決事項が detailed-design で放置 |

## 判定ルール

- Critical が1件でもあれば **不合格**
- Suggestion のみなら **条件付き合格**
- 指摘なしなら **合格**
