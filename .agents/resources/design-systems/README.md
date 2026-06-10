# デザインシステム リソース

UI 生成時に参照するデザイントークン仕様（getdesign.md 形式）のライブラリ。
各 `<name>/DESIGN.md` の frontmatter に `colors` / `typography` / `rounded` / `spacing` / `components` を持ち、CSS 変数へそのまま落とせる。

## 使い方（エージェント向け）

- 選択は **案件ごと**。pm-agent がイントークで候補を提示 → 人間が 1 つ選択する
- 選択結果は `projects/<project>/.pipeline-state.md` と `docs/README.md` に記録する
- frontend-worker は `.agents/resources/design-systems/<name>/DESIGN.md` を読み、トークンを CSS 変数化して UI に適用する（詳細は frontend-worker SKILL）
- フォントが proprietary な場合は DESIGN.md 内「Note on Font Substitutes」の無償代替（Inter / Geist / JetBrains Mono 等）を使う

## 代表的な候補（UI タイプ別）

| 用途 | おすすめ |
|------|----------|
| 汎用デフォルト（明暗両対応・無償フォント） | `vercel`（Geist） |
| ダーク・生産性 / 開発ツール | `linear`, `supabase`, `warp`, `raycast` |
| 明るい・B2B / 管理画面 / フォーム | `stripe`, `notion`, `intercom` |
| データ / ダッシュボード | `sentry`, `posthog`, `clickhouse` |
| 編集 / コンテンツ系 | `notion`, `wired`, `theverge` |

全候補はこのフォルダ直下のサブディレクトリ名（`linear.app`, `vercel`, `stripe` ...）で確認できる。

## 注意

- 各仕様は **inspired interpretation**（ブランドの商標・独自フォントそのものではない）。商用利用時は商標・フォントライセンスに留意する
- パイプラインのデフォルトは固定しない。指定がなければ pm-agent が用途に合う候補を提案する
