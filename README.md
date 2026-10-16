# gp-prod-health

APM・ログ・ブラウザログから **今週の本番状況レポート** を作る Claude Code スキルです。
遅いAPI・エラーの増減・フロントエンドのエラー・リリースの影響を **先週と比較** し、
チケットにすべき「注目点」を数字付きで、チャットにそのまま貼れる形で出力します（読み取り専用）。

A Claude Code skill that writes a weekly production health report: slowest endpoints, error trends,
front-end errors and deploy impact versus last week, plus the items worth a ticket, as a chat post.

## インストール / Install
```
/plugin marketplace add khoa-astec/gp-prod-health
/plugin install gp-prod-health@gp-prod-health
```

## 使い方 / Usage
```
/prod-health
「今週の本番状況をまとめて」
```
可観測性ツール（APM / ログ / RUM）へのアクセスは、各自がローカルで設定済みのもの（MCPサーバー、CLI、APIクライアント）を使います。
このリポジトリには接続情報・APIキーを含みません。

## 出力 / Output
- 遅いAPI TOP8（p50 / p90 / 件数・先週比）
- サーバーエラー TOP5（先週比）と、新しく出始めたエラー
- ブラウザのエラー（件数・影響ユーザー数）、リリース後に古いJSを読みに行って失敗するエラー
- リリース時刻とエラー・遅延の重なり
- 注目点（チケット候補）3〜5件。すべて数字付き。取得できなかった値は「取得不可」と明記

## 実績 / Results（2026-10 第2週、本番の実データ）
| 項目 | 結果 |
|---|---|
| 分析対象 | 約63万リクエスト・1週間分を先週と比較（数分で作成） |
| 発見1 | メッセージ削除APIのサーバーエラーが週355件で、全エラーの54%。原因箇所（null の扱い）まで特定 → チケット候補 |
| 発見2 | リリース後の「古いJSが読めない」エラーが1,087件・221人。うち18%がリリース直後2時間に集中 → 対策（旧ビルドをストレージに残す仕組み）の効果測定に利用 |
| 発見3 | 前週に約12,000件あったエラーが今週は解消していることを確認（前週比 -95%） |
| 発見4 | エラーが急増しているAPIを2件検出（4件→37件、26件→40件） |
