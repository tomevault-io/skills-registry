---
name: slack-channels-optimizer
description: Manage Slack channels and sidebar sections using the slack-opt CLI. Use when asked about Slack channels, organizing Slack sidebar, finding stale channels, or moving channels between groups. Use when this capability is needed.
metadata:
  author: ekroon
---

# Slack Channels Optimizer

Use the `slack-opt` CLI to inspect, organize, and optimize Slack channels and sidebar sections. The tool extracts credentials automatically from the Slack Desktop app on macOS.

## Available commands

```bash
slack-opt auth --json                   # Verify Slack credentials
slack-opt channels --json               # List all channels with section, lastRead, unreads, purpose
slack-opt channels --members --json     # Include member counts (slower, one API call per channel)
slack-opt sections --json               # List sidebar sections with channel counts
slack-opt export -o channels.md         # Export markdown report grouped by section
slack-opt assign --channel <names-or-ids> --section "<name>" --json  # Move channels to a section
```

## How to use

### Listing and analyzing channels

```bash
slack-opt channels --json
```

Each channel object has: `name`, `id`, `section`, `sectionId`, `lastRead` (unix timestamp string), `hasUnreads`, `purpose`, `topic`, `isPrivate`, `memberCount`.

- `lastRead` is null if never read — compare timestamps to find stale channels
- `hasUnreads` indicates unread messages
- `section` is null if the channel is ungrouped

### Moving channels between sections

```bash
# By name (comma-separated, no spaces)
slack-opt assign --channel general,random,engineering --section "Social" --json

# By ID
slack-opt assign --channel C01ABC,C02DEF --section "Work" --json
```

- Creates the section automatically if it doesn't exist
- Both channel names and IDs work

### Exporting

```bash
slack-opt export --json          # Full JSON
slack-opt export -o report.md   # Markdown grouped by sidebar section order
```

## Common workflows

**Find stale channels**: Run `slack-opt channels --json`, filter by `lastRead` older than N weeks.

**Find popular channels**: Run `slack-opt channels --members --json`, sort by `memberCount`.

**Reorganize sidebar**: List channels, group them by theme, then batch-assign with multiple `slack-opt assign` calls.

**Check unreads**: Filter channels JSON for `hasUnreads: true`.

## Constraints

- macOS only (credentials from Keychain + Slack Desktop app data)
- First run shows a Keychain prompt — user must approve
- Max ~29 sidebar sections in Slack
- Deleting a section orphans its channels — always reassign first
- `--members` flag is slow (one API call per channel)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekroon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
