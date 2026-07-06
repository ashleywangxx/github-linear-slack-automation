# github-linear-slack-automation

Automated weekly deploy summary agent for `gocrisp/app-aurora`.

Each Monday at ~5pm, a Claude scheduled task:
1. Finds the last prod release tag on `gocrisp/app-aurora`
2. Collects all PRs merged to `develop` since that release
3. Maps each PR to a Linear issue (best-effort, via branch/title ID matching)
4. Composes a structured Slack draft in `#es-temp-aurora-fifa` for review before the weekly deploy

The draft lands in Slack "Drafts & Sent" — Ashley reviews and hits send. Nothing posts automatically.

## Setup

### Prerequisites

- [Claude desktop app](https://claude.ai/download) installed and running
- `gh` CLI authenticated: `gh auth login`
- Slack MCP connected in claude.ai connector settings
- Linear MCP connected in claude.ai connector settings (optional — skipped gracefully if missing)

### Install the scheduled task

Copy `scheduled-tasks/weekly-aurora-deploy-summary/SKILL.md` into:

```
~/.claude/scheduled-tasks/weekly-aurora-deploy-summary/SKILL.md
```

The Claude app picks it up automatically. You can manage it from the **Scheduled** section in the sidebar.

### First run

Trigger "Run now" from the sidebar to pre-approve the tools (`gh`, Slack MCP, Linear MCP) so future scheduled runs don't pause on permission prompts.

## Files

```
scheduled-tasks/
  weekly-aurora-deploy-summary/
    SKILL.md        # Self-contained task prompt — edit this to change behavior
```

## Slack message format

```
🚀 Weekly Deploy Summary — [date]

PRs shipping in this deploy (merged to `develop` since [last release tag]):
• #123 Some PR title — @author → ENG-456: Linear issue title (In Progress)
• #124 Another PR — @author  → no Linear issue

Latest releases:
• v1.22.0 — Jun 30 — compare link
• v1.21.0 — Jun 23 — compare link

5 PRs total • Compare: v1.22.0...develop
```

## Customization

Edit `SKILL.md` to change:
- Target repo (`gocrisp/app-aurora`)
- Slack channel ID (`C0B01UG1UTF`)
- Number of releases shown
- Message format
