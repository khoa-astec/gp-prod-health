---
name: prod-health
description: Write a weekly production health report from APM, logs and RUM/browser logs. Use when the user says "/prod-health", "今週の本番状況", "weekly health report", or wants to know what got slower or broke this week. Compares with the previous week, lines spikes up with deploys, and lists the items worth a ticket, as a plain-text team chat post. Read-only.
---

# Weekly production health report

Answer "what got worse this week, who did it hit, and what should we fix" with numbers. Read-only; uses whatever access the user already has to the observability tool (an MCP server, CLI or API client configured locally). Nothing is stored in this repo.

## Inputs
- Week window (default: last 7 full days, local timezone) and the comparison window (previous 7 days).
- Service and environment names (ask once; e.g. APM service `web`, env `prod`).
- Deploy timestamps: from your deploy system (e.g. ECS task-definition `registeredAt`, CI pipeline runs) or deploy events in the observability tool.

## 1. Latency
- Root request spans per endpoint: p50, p90, count. Keep endpoints with enough traffic (e.g. ≥500 requests/week).
- Top 8 by p90, with the change vs the previous week. Flag p90 increases ≥30% or new entries in the top list.

## 2. Server errors
- Error count per endpoint (5xx or error spans), top 5, with week-over-week change.
- New error messages that did not appear last week (cluster by message pattern).

## 3. Front end
- Browser error totals and affected users, top 3 messages.
- Stale-asset errors after deploys ("Failed to fetch dynamically imported module", "Importing a module script failed"): count and users. They mean open tabs broke when a deploy replaced the old JS files.

## 4. Deploy impact
List deploys in the window. For each, check the hour after it: error spikes, latency jumps, stale-asset errors. A daytime deploy with a spike is worth calling out.

## 5. Write the post
Plain text (chat apps don't render tables well), ≤40 lines, in the team's language:
```
■今週の本番状況（MM/DD〜MM/DD）
・遅いAPI TOP: <endpoint> p90 X秒（先週比 +Y%）…
・エラー: …
・フロント: …
・リリース影響: …
■注目点（チケット候補）
1. … （数字）
```
Every line carries a number. Mark anything you couldn't fetch as 取得不可 instead of guessing. Offer ticket drafts for the 注目点; create them only after approval.
