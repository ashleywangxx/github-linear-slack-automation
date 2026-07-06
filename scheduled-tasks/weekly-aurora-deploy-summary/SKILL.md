---
name: weekly-aurora-deploy-summary
description: Weekly deploy summary draft for #es-temp-aurora-fifa — PRs shipping in deploy
---

You are generating a weekly deploy summary for the gocrisp/app-aurora GitHub repository. Your job is to collect what's shipping this week and compose a Slack draft in #es-temp-aurora-fifa for Ashley to review and send before the weekly deploy.

## Step 1 — Find the last production release

Run: `gh release list --repo gocrisp/app-aurora --limit 10 --json tagName,publishedAt,url`

The latest release tagged with a version like `vX.Y.Z` is the last prod release. Note its tag name and publishedAt timestamp.

Also confirm with: `gh api repos/gocrisp/app-aurora/git/refs/tags --jq '[.[] | select(.ref | startswith("refs/tags/deploy/prod/"))] | sort_by(.ref) | last'`

## Step 2 — Collect PRs merged to develop since last prod release

Run: `gh pr list --repo gocrisp/app-aurora --state merged --base develop --limit 50 --json number,title,author,mergedAt,url,headRefName`

Filter to PRs where:
1. `mergedAt` is after the last prod release `publishedAt` timestamp
2. `author.login` is one of: `ashleywangxx`, `abhilashkoneru-crisp`, `carolbaobao`

Exclude all PRs from any other author.

For each qualifying PR, extract: PR number, title, author login, merge date, and URL.

## Step 3 — Determine the deploy date

The deploy date is always the Tuesday immediately following the day this task runs (Monday). Format it as: `Tuesday, [Month] [D], [YYYY]` — e.g. `Tuesday, July 8, 2025`. Compute this from the current date.

## Step 4 — Compose and post the Slack draft

Compose a clean, professional Slack message in this format:

```
<@U08M2NJ3U5Q>

*Weekly Deploy Summary — Tuesday, [Month D, YYYY]*

Here are the PRs shipping in this week's deploy:

• <PR URL|#[number] — [title]> — @[author]
• <PR URL|#[number] — [title]> — @[author]
...

_[N] PRs shipping • Merged to `develop` since [lastProdTag]_
```

Guidelines:
- Tag Sameer using `<@U08M2NJ3U5Q>` at the top so he's notified
- Use Slack hyperlink format `<url|display text>` for each PR
- Keep PR titles as-is — do not truncate or paraphrase
- No Linear issue references
- No "Latest releases" section
- Tone should be clean and direct — no filler phrases

Use the `slack_send_message_draft` MCP tool with:
- channel: `C0B01UG1UTF` (this is the #es-temp-aurora-fifa channel)
- The fully composed message above

This creates a draft that Ashley can review in Slack Drafts & Sent before sending — do NOT post it directly.

## Error handling

- If `gh` CLI is not authenticated or the repo is inaccessible, stop and output an error message explaining what's blocked.
- If no PRs from the three authors are found since the last release, compose the draft anyway noting "No new PRs this week."
- If the Slack MCP draft tool fails because a draft already exists, note this in your output so Ashley can clear the existing draft and re-run.

## Output

After posting the draft, output a brief confirmation:
- Last prod release found: [tag]
- PRs collected: [N] (from ashleywangxx, abhilashkoneru-crisp, carolbaobao only)
- Deploy date in draft: [Tuesday date]
- Slack draft: created in #es-temp-aurora-fifa ✓
