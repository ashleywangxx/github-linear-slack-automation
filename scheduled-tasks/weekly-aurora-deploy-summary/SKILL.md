---
name: weekly-aurora-deploy-summary
description: Weekly deploy summary draft for #es-temp-aurora-fifa — PRs, releases, Linear links
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

Exclude all PRs from any other author. These three are the only team members whose changes are included in the deploy summary.

For each qualifying PR, extract: PR number, title, author login, merge date, URL, and branch name.

## Step 3 — Try to map PRs to Linear issues (best-effort)

For each shipping PR, look for a Linear issue ID in:
- The branch name (pattern: letters-digits, e.g. `ENG-123`, `AURORA-456`)
- The PR title (same pattern)

If you find a Linear issue ID, use the Linear MCP tool to look up the issue title and status. If Linear MCP is not available or the lookup fails, skip gracefully — mark that PR as "no Linear issue found."

## Step 4 — Get latest releases

Run: `gh release list --repo gocrisp/app-aurora --limit 5 --json tagName,publishedAt,url`

Format these as a short list.

## Step 5 — Compose and post the Slack draft

Compose a Slack message using this format (plain Slack markdown, no HTML):

```
🚀 *Weekly Deploy Summary — [date]*

*PRs shipping in this deploy* (merged to `develop` since [last release tag]):
• <[PR#] Title|url> — @author [→ LINEAR-123: Issue title (Status) | no Linear issue]
• ... (one bullet per PR)

*Latest releases:*
• [vX.Y.Z] — [date] — <compare link>
• ...

_[N] PRs total • Compare: <https://github.com/gocrisp/app-aurora/compare/[lastProdTag]...develop|[lastProdTag]...develop>_
```

Use the `slack_send_message_draft` MCP tool with:
- channel: `C0B01UG1UTF` (this is the #es-temp-aurora-fifa channel)
- The fully composed message above

This creates a draft that Ashley can review in Slack Drafts & Sent before sending — do NOT post it directly.

## Error handling

- If `gh` CLI is not authenticated or the repo is inaccessible, stop and output an error message explaining what's blocked.
- If no PRs from the three authors are found since the last release, compose the draft anyway noting "No new PRs merged since [last release tag] from the deploy team."
- If the Slack MCP draft tool fails because a draft already exists, note this in your output so Ashley can clear the existing draft and re-run if needed.
- If Linear MCP is unavailable, skip the Linear step and list PRs without issue links — do not fail the whole task.

## Output

After posting the draft, output a brief confirmation:
- Last prod release found: [tag]
- PRs collected: [N] (from ashleywangxx, abhilashkoneru-crisp, carolbaobao only)
- Linear mappings: [N found / N attempted]
- Slack draft: created in #es-temp-aurora-fifa ✓
