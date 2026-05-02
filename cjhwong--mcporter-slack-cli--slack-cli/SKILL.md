---
name: slack-cli
description: > Use when this capability is needed.
metadata:
  author: cjhwong
---

# Slack CLI

Run Slack operations via `slack-cli`.

If any command fails (not found, auth error, etc.), see `references/troubleshooting.md`.

## Quick Reference

| Task | Command |
|------|---------|
| List channels | `slack-cli channels-list --channel-types public_channel` |
| Read messages | `slack-cli conversations-history --channel-id '#channel' --limit 7d` |
| Read thread | `slack-cli conversations-replies --channel-id '#channel' --thread-ts <ts> --limit 50` |
| Search | `slack-cli conversations-search-messages --search-query "text"` |
| Post message | `slack-cli conversations-add-message --channel-id '#channel' --text "text"` |

## Task Guides

### Reading Data
For channels, messages, threads, and search → see `references/reading.md`

### Writing Data
For posting messages, reactions, and user groups → see `references/writing.md`

### Managing Users
For searching users and user groups → see `references/users.md`

### Daemon & Advanced
For server management, pagination, output formats, and global flags → see `references/daemon.md`

## Gotchas

- `--limit` on `conversations-replies` silently drops replies older than the time range — only the parent message is guaranteed. Use a message count (e.g. `--limit 100`) to get the full thread.
- `usergroups-users-update` **replaces** the entire member list. Fetch current members first, merge, then update.
- Write commands (`conversations-add-message`, `reactions-add`) require env vars: `SLACK_MCP_ADD_MESSAGE_TOOL=true`, `SLACK_MCP_REACTION_TOOL=true`. If not set, the command fails. See `references/troubleshooting.md`.
- Attachment downloads only work for Slack-hosted files. External links (Google Docs, Dropbox) return 401 — use the URL from message output instead.
- `-o json` and `-o markdown` produce CSV identical to default. Only `-o raw` returns MCP JSON envelope.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cjhwong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
