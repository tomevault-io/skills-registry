---
name: bento
description: Manage Bento email marketing (subscribers, tags, broadcasts, sequences, templates, events, stats). Two interfaces available — Bento CLI for human-facing operations, MCPorter-wrapped MCP for agent/programmatic use. Always use --json flag when parsing output. Use when this capability is needed.
metadata:
  author: budivoogt
---

# Bento

Email marketing platform. Two tools available — use the right one for the job.

## Which Tool to Use

| Task | Tool | Why |
|------|------|-----|
| Interactive auth, profile switching | **Bento CLI** | CLI has profile management, MCP doesn't |
| CSV subscriber imports | **Bento CLI** | CLI has `--dry-run`, `--limit`, `--sample` safety flags |
| Bulk tag add/remove from file | **Bento CLI** | CLI accepts `--file` for batch operations |
| Agent automation / scripting | **MCPorter (MCP)** | JSON output, no profile state needed |
| Reading/updating email templates | **MCPorter (MCP)** | CLI has no template read/update commands |
| Listing automations (sequences, workflows) | **MCPorter (MCP)** | CLI has no automations command |
| Creating broadcasts | **Either** | Both work; MCP requires `fromEmail` param |
| Subscriber lookup | **Either** | CLI has more filter options (tag, field, pagination) |

## Agent Rules

- Always use `--json` flag with Bento CLI for parseable output.
- Always use `--json` flag with MCPorter for parseable output.
- Never print API keys or credentials.
- Broadcasts are always created as drafts. They must be sent from the Bento web dashboard.
- When updating email templates via MCP, HTML must include `{{ visitor.unsubscribe_url }}` for compliance.
- Never use `--confirm` without user approval first. Prefer `--dry-run` to preview bulk operations.
- **NEVER send, publish, or save a broadcast/template without explicit user approval.** Always show the content and ask for confirmation before executing any write operation (create_broadcast, update_email_template, subscribers import --confirm).

## Prerequisites

- Node.js 18+
- Bento account with API access
- API credentials from Bento Settings > Team Settings > API Keys:
  - `BENTO_PUBLISHABLE_KEY`
  - `BENTO_SECRET_KEY`
  - `BENTO_SITE_UUID`

---

## Bento CLI

Package: `@bentonow/bento-cli` (install globally with `npm i -g @bentonow/bento-cli`)

- Docs: https://github.com/bentonow/bento-cli
- Reference: https://bentonow.com/docs/integrations/cli

### Auth & Profiles

```bash
# Interactive login
bento auth login

# Non-interactive (CI/scripting)
bento auth login --publishable-key "pk_..." --secret-key "sk_..." --site-uuid "..."

# Check status
bento auth status

# Manage multiple accounts
bento profile add <name> --publishable-key "..." --secret-key "..." --site-uuid "..."
bento profile list
bento profile use <name>
bento profile remove <name> [-y]
```

### Subscribers

```bash
# Search by email, tag, field, or UUID
bento subscribers search --email "user@example.com" --json
bento subscribers search --tag "trial" --json
bento subscribers search --field "plan=pro" --json
bento subscribers search --uuid "abc-123" --json
bento subscribers search --email "user@example.com" --page 2 --per-page 50 --json

# Import from CSV (email column required)
bento subscribers import ./contacts.csv --dry-run        # Preview first
bento subscribers import ./contacts.csv --limit 10 --confirm --json

# Tag management (single email or file)
bento subscribers tag -e user@example.com --add "vip,newsletter" --json
bento subscribers tag -e user@example.com --remove "trial" --json
bento subscribers tag -f ./emails.csv --add "webinar" --dry-run

# Subscribe / Unsubscribe
bento subscribers subscribe -e user@example.com --json
bento subscribers unsubscribe -e user@example.com --json
bento subscribers unsubscribe -f ./churned.csv --confirm --json
```

### Tags

```bash
bento tags list --json
bento tags create "tag-name"
bento tags delete "tag-name" [--confirm]
```

### Custom Fields

```bash
bento fields list --json
bento fields create "field_key"    # camelCase or snake_case auto-converts to display name
```

### Events

```bash
bento events track -e "user@example.com" --event "completed_onboarding" --json
bento events track -e "user@example.com" --event "signup" -d '{"source": "blog"}' --json
```

### Broadcasts

```bash
bento broadcasts list --json
bento broadcasts create \
  --name "Internal Name" \
  --subject "Subject Line" \
  --content "Email body text or HTML" \
  --type plain|html|markdown \
  --from-name "Sender Name" \
  --from-email "sender@example.com" \
  --include-tags "tag1,tag2" \
  --exclude-tags "tag3" \
  --batch-size 500
```

### Stats

```bash
bento stats site --json
```

### Global Flags

| Flag | Description |
|------|-------------|
| `--json` | Machine-readable JSON output. **Always use for agent work.** |
| `--quiet` | Suppress non-error output |
| `--verbose` | Debug mode with full API request/response |
| `--dry-run` | Preview without making changes (subscribers import/tag) |
| `--confirm` | Skip confirmation prompts (bulk operations) |
| `--limit <n>` | Process only first N items |
| `--sample <n>` | Show N sample items in preview |

---

## MCPorter (MCP via CLI)

The Bento MCP server (`@bentonow/bento-mcp`) exposes 13 tools not all available in the CLI. Use [MCPorter](https://github.com/steipete/mcporter) to call them as CLI commands without loading MCP tool schemas into agent context.

- Docs: https://bentonow.com/docs/integrations/mcp
- Source: https://github.com/bentonow/bento-mcp

### Setup

Register the Bento MCP server with MCPorter. Project-level at `config/mcporter.json` or globally at `~/.mcporter/mcporter.json`:

```json
{
  "mcpServers": {
    "bento": {
      "command": "npx -y @bentonow/bento-mcp",
      "description": "Bento email marketing",
      "env": {
        "BENTO_PUBLISHABLE_KEY": "your-publishable-key",
        "BENTO_SECRET_KEY": "your-secret-key",
        "BENTO_SITE_UUID": "your-site-uuid"
      }
    }
  }
}
```

### Calling Tools

```bash
npx mcporter call bento.<tool_name> [key=value ...] --json
```

### All 13 MCP Tools

#### Subscribers

```bash
# Look up subscriber by email or UUID
npx mcporter call bento.get_subscriber email="user@example.com" --json
npx mcporter call bento.get_subscriber uuid="abc-123" --json

# Batch import (up to 1000, does NOT trigger automations)
npx mcporter call bento.batch_import_subscribers \
  subscribers='[{"email":"a@b.com","firstName":"Alice","tags":"trial"}]' --json
```

#### Tags

```bash
npx mcporter call bento.list_tags --json
npx mcporter call bento.create_tag name="new-tag" --json
```

#### Custom Fields

```bash
npx mcporter call bento.list_fields --json
npx mcporter call bento.create_field key="company_name" --json
```

#### Events

```bash
# Standard event
npx mcporter call bento.track_event \
  email="user@example.com" type="completed_onboarding" --json

# Purchase event (requires value.currency, value.amount in cents, unique.key)
npx mcporter call bento.track_event \
  email="user@example.com" type="\$purchase" \
  details='{"unique":{"key":"order_123"},"value":{"currency":"USD","amount":4999}}' --json
```

Purchase event types that trigger special validation: `$purchase`, `purchase`, `order`, `order_complete`, `event_sale`.

#### Stats

```bash
npx mcporter call bento.get_site_stats --json
```

#### Broadcasts

```bash
npx mcporter call bento.list_broadcasts --json

npx mcporter call bento.create_broadcast \
  name="Internal Name" \
  subject="Subject Line" \
  content="Email body" \
  type="plain" \
  fromName="Sender" \
  fromEmail="sender@example.com" --json
```

Optional params: `inclusiveTags`, `exclusiveTags`, `segmentId`, `batchSizePerHour`.

#### Automations (MCP only)

```bash
# List all sequences and workflows
npx mcporter call bento.list_automations --json

# Filter by type
npx mcporter call bento.list_automations type="sequences" --json
npx mcporter call bento.list_automations type="workflows" --json
```

Returns sequence/workflow IDs and their associated `email_templates` array with template IDs, subjects, and stats.

#### Email Templates (MCP only)

```bash
# Get template content by ID (returns name, subject, HTML, stats)
npx mcporter call bento.get_email_template id=12345 --json

# Update template subject and/or HTML
npx mcporter call bento.update_email_template \
  id=12345 \
  subject="New Subject Line" \
  html="<p>New content</p><a href=\"{{ visitor.unsubscribe_url }}\">Unsubscribe</a>" --json
```

Template updates take effect immediately for future sends. HTML **must** include `{{ visitor.unsubscribe_url }}`.

---

## Common Workflows

### Push email content to a sequence

1. List automations to get template IDs:
   ```bash
   npx mcporter call bento.list_automations type="sequences" --json
   ```
2. Update each template slot:
   ```bash
   npx mcporter call bento.update_email_template id=TEMPLATE_ID subject="..." html="..." --json
   ```
3. Adjust timing in the Bento web dashboard (API cannot change sequence timing).

### Pull email content back from a sequence

1. Get template IDs from `list_automations`
2. Fetch each template:
   ```bash
   npx mcporter call bento.get_email_template id=TEMPLATE_ID --json
   ```
3. The `html` field contains the full email content.

### Create broadcast drafts

```bash
# Via CLI
bento broadcasts create --name "Campaign" --subject "Subject" --content "Body" --type plain --from-name "Name"

# Via MCPorter
npx mcporter call bento.create_broadcast name="Campaign" subject="Subject" content="Body" type="plain" fromName="Name" fromEmail="sender@example.com" --json
```

Note: Broadcasts created via API are always drafts. Send from the Bento web dashboard.

### Track a signup event (trigger automations)

```bash
npx mcporter call bento.track_event email="new@user.com" type="\$signup" --json
```

Events can trigger Bento automations. Use `batch_import_subscribers` for bulk imports that should NOT trigger automations.

---

## Capability Comparison

| Capability | CLI | MCP (via MCPorter) |
|------------|-----|-------------------|
| Subscriber search (email, tag, field, UUID) | Yes (richer filters) | Yes (email/UUID only) |
| Subscriber CSV import | Yes (`--dry-run`, `--limit`) | Yes (JSON array, max 1000) |
| Subscriber tag management | Yes (file-based batch) | Via batch_import |
| Subscribe/Unsubscribe | Yes | No |
| Tags list/create | Yes | Yes |
| Tags delete | Yes | No |
| Fields list/create | Yes | Yes |
| Event tracking | Yes | Yes (+ purchase validation) |
| Broadcasts list/create | Yes | Yes |
| Stats | Yes | Yes |
| **Automations list** | No | **Yes** |
| **Email template get** | No | **Yes** |
| **Email template update** | No | **Yes** |
| Profile management | Yes | N/A |
| Dry run / safety flags | Yes | No |

---

## Limitations

- **Broadcasts list endpoint** returns only sent broadcasts, not drafts. Drafts created via API won't appear in `list_broadcasts`.
- **No single-broadcast-by-ID endpoint.** `list_broadcasts` returns all; filter client-side.
- **Sequence timing** cannot be set via API. Must configure delays in the Bento web dashboard.
- **No automation create/update** via API. Only listing is supported.
- **Template update is immediate.** No draft/preview — changes go live for future sends.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/budivoogt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
