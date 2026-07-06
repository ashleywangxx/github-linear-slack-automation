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

For each qualifying PR, extract: PR number, title, author login, and URL.

## Step 3 — Determine the deploy date

The deploy is always the Tuesday immediately following the day this task runs (Monday). Format it as: `[Month] [D]` — e.g. `July 8`. Compute this from the current date.

## Step 4 — Compose and post the Slack draft

Compose the message exactly in this format:

```
<@U08M2NJ3U5Q> — here are the PRs shipping tomorrow ([Month D]) in this week's deploy:

• <url|#[number] [title]> — @[author]
• <url|#[number] [title]> — @[author]
...

_[N] PRs • develop since [lastProdTag]_
```

Guidelines:
- The opening line tags Sameer naturally — do not add a separate header or bold title
- Use Slack hyperlink format `<url|display text>` for each PR — this prevents link previews
- Keep PR titles as-is
- No Linear references, no "Latest releases" section
- Tone: direct and clean

Use the `slack_send_message_draft` MCP tool with:
- channel: `C0B01UG1UTF`
- unfurl_links: false
- unfurl_media: false
- The fully composed message above

Do NOT post directly — this creates a draft for Ashley to review.

## Error handling

- If `gh` CLI is inaccessible, stop and report the error.
- If no qualifying PRs are found, draft the message noting "No new PRs this week."
- If a Slack draft already exists and the tool errors, note it so Ashley can clear and re-run.

## Output

After posting the draft, output:
- Last prod release: [tag]
- PRs collected: [N]
- Deploy date: [Tuesday date]
- Slack draft: created in #es-temp-aurora-fifa ✓
