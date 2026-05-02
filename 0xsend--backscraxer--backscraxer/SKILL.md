---
name: backscraxer
description: CLI tool and local SQLite database for ingesting and analyzing Twitter/X data from twitterapi.io. Use when this capability is needed.
metadata:
  author: 0xsend
---

# backscraxer Agent Skill

CLI tool and local SQLite database for ingesting and analyzing Twitter/X data
from twitterapi.io.

## Single-User-First Default

The default agent prompt for fetching tweets:

```
/backscraxer fetch the most recent 5 tweets from @send2vic
```

All fetch commands (`fetch:user`, `fetch:users`) follow the DB-first workflow:
API fetch -> DB persist -> DB query -> output. Returned results are always derived
from the local SQLite database, never direct API passthrough.

Multi-user fetch is explicit opt-in via `fetch:users` with `--max-users` (default 1).

## Intent Routing (Required)

Treat these intents as setup/setup-repair actions, not data commands:

- `/backscraxer install`
- `/backscraxer setup`
- `/backscraxer configure`

For those intents:

1. Run exactly one installer command in this order:
   - `backscraxer install` (preferred; supports standalone binaries)
   - `bash ./scripts/install-skill.sh` (if repo-local script exists)
   - `bash ~/.codex/skills/backscraxer/install-skill.sh` (or matching Claude/Cursor path)
2. Run `bash ./scripts/check-env.sh --mode api` immediately after the installer attempt (even if installer fails).
3. Return only the next required action if setup is still incomplete (one action at a time).

## Execution Modes (Required)

- **Live fetch mode**: `fetch:*`, `ingest:*`.
  Requires: CLI availability, `TWITTERAPI_IO_KEY`, and outbound network access to `api.twitterapi.io`.
- **Offline analysis mode**: `report:users`, `db:*`, `docs:get-endpoints`, direct sqlite queries.
  Works without API key and without network.

If live-mode preflight fails on network, do not auto-switch modes. Return one concise remediation action.

## First Action (Required)

Before running commands, classify the request into exactly one mode:

1. Setup intent: `install`, `setup`, `configure`
2. Live mode: `fetch:*`, `ingest:*`
3. Offline mode: `report:users`, `db:*`, `docs:get-endpoints`, ad-hoc sqlite analysis

Then select runtime paths once per request:

1. CLI binary: `backscraxer` if available, otherwise `./dist/backscraxer` when present.
2. DB path: explicit `--db` if user provided one, otherwise `~/.backscraxer/data.db`.

Do not run live-mode preflight for offline-only requests.

## Deterministic Tool-Call Ladder (Required)

Use this exact call order to keep behavior consistent.

### Setup intents

1. Run installer from the existing routing order.
2. Run `bash ./scripts/check-env.sh --mode api` even if installer returned an error.
3. Return the next required action (single step) if still incomplete.

### Live mode intents

1. Run mandatory preflight once:
   `bash ./scripts/check-env.sh --mode api --format json --checks cli,key,network`
2. If preflight passes, run exactly one target `backscraxer` command.
3. If key missing, provide key-load remediation, then retry the original command once.
4. If network fails, stop with one concise remediation step.

### Offline mode intents

1. Resolve time window first (if present in user request).
2. Run one canonical query/command first (no speculative SQL).
3. If query errors with `no such column`, inspect schema once, then rerun corrected query once.
4. If count query returns `0`, run one coverage query before concluding.
5. Report result as local DB scope only.
6. Do not auto-run live ingest/fetch after an offline query unless the user explicitly asked for ingestion.
   If data is missing, offer exactly one ingest command and ask for confirmation.

## Response Contract (Required)

For every user-facing answer, include:

1. `Mode`: `setup`, `live`, or `offline`
2. `Resolved window`: explicit UTC timestamps when time filtering applies
3. `Result`: primary metric or failure cause
4. `Scope`: explicitly `local DB` for offline answers
5. `Next action`: exactly one remediation or follow-up command when needed

Keep the final response concise; do not expose raw stack traces as the primary answer.
Do not add extra analytics (for example, YoY deltas) unless the user asked for comparison.

## Mandatory API Preflight (Before Live Commands)

Before any live-mode command, run:

```bash
bash ./scripts/check-env.sh --mode api --format json --checks cli,key,network
```

Interpretation and recovery:

1. If `cli` fails, auto-run installer flow from the intent-routing order above, then retry preflight once.
   If CLI is still not in PATH but `./dist/backscraxer` exists, use `./dist/backscraxer` for all command examples in this session.
2. If `key` fails, reply with:
   - `I need your TwitterAPI key to fetch live tweets.`
   - `source ./scripts/source-session-env.sh`
   - fallback only if needed: `export TWITTERAPI_IO_KEY="..."`
   Then retry the original command after the key is loaded.
3. If `network` fails, return one concise fix step and stop.
4. Never surface raw `command not found` / shell exit codes as the primary user message.

## Natural-Language Time Resolution

When users provide natural-language time windows, convert to explicit UTC ISO timestamps before running commands.
Always echo the resolved timestamps in the response.

Example:
- `before last week` -> `to=2026-02-02T00:00:00Z` (based on current date context)

### Month Name Disambiguation (Required)

For bare month names without a year (for example, `in january`):

1. Resolve to the most recent past instance of that month relative to the current date context.
2. Always echo the exact UTC range with year using one of these canonical forms:
   - inclusive: `from=YYYY-MM-01T00:00:00Z`, `to=YYYY-MM-(last day)T23:59:59Z`
   - half-open: `from=YYYY-MM-01T00:00:00Z`, `to_exclusive=YYYY-(next month)-01T00:00:00Z`
3. Never mix inclusive and half-open bounds in the same query.
4. If the user states a year explicitly, use that year exactly.

## Prerequisites

- `TWITTERAPI_IO_KEY` environment variable set with a valid twitterapi.io API key
- **Active twitterapi.io account with sufficient credits** (pay-as-you-go: $0.15/1k tweets, $0.18/1k profiles)
- **Network access to `api.twitterapi.io`** (HTTPS outbound on port 443)
- Node.js >= 18
- SQLite3 CLI (for running analysis queries directly)
- If setup is missing, run bundled installer: `bash ~/.codex/skills/backscraxer/install-skill.sh` (or the equivalent Claude/Cursor path)
- Runtime API commands resolve keys in this order:
  1) `TWITTERAPI_IO_KEY` from current environment
  2) `~/.backscraxer/session_env.sh`
  3) saved skill session files (Codex/Claude/Cursor)
  You can still source a session file explicitly when needed:
  `source ~/.codex/skills/backscraxer/session_env.sh`

## Offline Analysis Guardrails (Required)

When answering offline DB questions (for example, `how many tweets from nasa in january`):

1. Normalize handle filters:
   - strip leading `@`
   - compare with `LOWER(u.user_name)`
2. Use the canonical join for per-user tweet queries:
   - `tweets.author_id = users.id`
3. Use canonical column names:
   - users: `id`, `user_name`, `created_at`
   - tweets: `id`, `author_id`, `created_at`, `text`
4. Do not guess column names (`screen_name`, `user_screen_name`, etc.).
5. If a SQL query returns `no such column`, run schema inspection once, then rerun with corrected SQL.

Preferred schema checks:

```bash
sqlite3 ~/.backscraxer/data.db ".schema users"
sqlite3 ~/.backscraxer/data.db ".schema tweets"
```

Canonical count query (user + date range):

```bash
sqlite3 -header -column ~/.backscraxer/data.db "
SELECT COUNT(*) AS tweet_count
FROM tweets t
JOIN users u ON u.id = t.author_id
WHERE LOWER(u.user_name) = LOWER('nasa')
  AND t.created_at >= '2026-01-01T00:00:00Z'
  AND t.created_at <= '2026-01-31T23:59:59Z';
"
```

Canonical coverage query (to explain zero results):

```bash
sqlite3 -header -column ~/.backscraxer/data.db "
SELECT
  MIN(t.created_at) AS earliest,
  MAX(t.created_at) AS latest,
  COUNT(*) AS total
FROM tweets t
JOIN users u ON u.id = t.author_id
WHERE LOWER(u.user_name) = LOWER('nasa');
"
```

If `tweet_count = 0`, state that this is a **local DB result** (not a claim about all of X),
then offer one ingest command with the same resolved date window.

## Quickstart Workflow

### 1. Fetch recent tweets (single user, DB-first)

```bash
backscraxer fetch:user --user-name send2vic --limit 5 --format json
```

Default `--limit` is 5 when omitted. Output is always from the local DB after ingestion.

### 2. Ingest tweets by user timeline

```bash
backscraxer ingest:user \
  --user-name <handle> \
  --from 2025-01-01T00:00:00Z \
  --to 2025-06-30T23:59:59Z \
  --limit 500 \
  --with-media \
  --db ./data.db
```

### 3. Ingest tweets by search query

```bash
backscraxer ingest:search \
  --query "from:handle keyword" \
  --query-type Latest \
  --from 2025-01-01T00:00:00Z \
  --to 2025-06-30T23:59:59Z \
  --db ./data.db
```

### 4. Apply analytics views

```bash
sqlite3 ./data.db < src/db/views.sql
```

### 5. Run the analysis report

```bash
sqlite3 -header -column ./data.db < examples/analysis_queries.sql
```

## Command Reference

| Command | Purpose | Requires API Key |
|---------|---------|-----------------|
| `fetch:user` | Fetch tweets for a single user (DB-first) | Yes |
| `fetch:users` | Fetch tweets for multiple users (explicit opt-in) | Yes |
| `ingest:user` | Ingest tweets from a user timeline | Yes |
| `ingest:search` | Ingest tweets matching a search query | Yes |
| `report:users` | Report per-user metrics from DB (table/json/csv) | No |
| `docs:get-endpoints` | List available API endpoints | No |
| `db:stats` | Show database statistics | No |
| `db:prune` | Remove old data (dry-run by default) | No |

## Key Parameters

| Flag | Description | Default |
|------|-------------|---------|
| `--user-name` | Twitter handle (leading `@` auto-stripped) | required for fetch/ingest |
| `--from` | Start of date range (ISO 8601 UTC) | None (unbounded) |
| `--to` | End of date range (ISO 8601 UTC) | None (unbounded) |
| `--limit` | Maximum tweets to fetch/ingest within date range | None (unbounded); `fetch:user`/`fetch:users` default to 5 |
| `--max-users` | Maximum users to process (`fetch:users` only) | 1 |
| `--with-media` | Download media files locally | false |
| `--out-media-dir` | Directory for downloaded media | `~/.backscraxer/media` |
| `--db` | SQLite database path | `~/.backscraxer/data.db` |
| `--resume` / `--no-resume` | Resume from checkpoint | `--resume` (default) |
| `--format` | Output format (`table`, `json`; `report:users` also supports `csv`) | `table` |

## Key Behaviors

- **Date range is authoritative over limit**: when both `--from`/`--to` and `--limit`
  are provided, date range is the hard boundary. Limit only caps the count of in-range
  tweets.
- **Checkpoint resume**: ingestion automatically resumes from the last successful page
  if interrupted. Use `--no-resume` to start fresh.
- **Idempotent writes**: re-running the same ingestion window does not create duplicates.
- **Pruning safety**: `db:prune` runs in dry-run mode by default. Pass `--apply` to
  execute deletions.

## Analytics Views

After applying `src/db/views.sql`, the following views are available:

| View | Description |
|------|-------------|
| `analytics_tweet_rollup` | Per-tweet metrics: engagements, efficiency, tweet type, media class |
| `analytics_daily_cadence` | Daily aggregate volume and engagement KPIs |
| `analytics_weekly_cadence` | Weekly aggregate KPIs (ISO Monday week start) |
| `analytics_theme_per_tweet` | Per-tweet theme and sentiment labels |
| `analytics_theme_rollup` | Theme/sentiment grouped performance |
| `analytics_media_mix` | Performance by media class (photo/video/mixed/text_only) |

### Querying Views

```sql
-- Top tweets by engagement efficiency
SELECT id, SUBSTR(text, 1, 80), total_engagements, view_count,
       ROUND(engagements_per_1k_views, 2) AS efficiency
FROM analytics_tweet_rollup
WHERE engagements_per_1k_views IS NOT NULL
ORDER BY engagements_per_1k_views DESC
LIMIT 10;

-- Daily cadence last 7 days
SELECT post_date, tweet_count, ROUND(avg_engagements, 1)
FROM analytics_daily_cadence
ORDER BY post_date DESC
LIMIT 7;

-- Theme performance
SELECT theme, SUM(tweet_count) AS tweets,
       ROUND(SUM(total_engagements) * 1.0 / SUM(tweet_count), 1) AS avg_eng
FROM analytics_theme_rollup
GROUP BY theme
ORDER BY avg_eng DESC;

-- Media mix comparison
SELECT media_class, tweet_count, ROUND(avg_engagements, 1)
FROM analytics_media_mix
ORDER BY avg_engagements DESC;
```

## Key Metric Definitions

- **total_engagements**: `likes + retweets + replies + quotes + bookmarks` (nulls coalesced to 0)
- **engagements_per_1k_views**: `(total_engagements * 1000) / view_count` when `view_count > 0`, else NULL
- **tweet_type**: `retweet`, `reply`, `quote`, or `original` (derived from `is_retweet`, `is_reply`, `is_quote` flags)
- **media_class**: `photo`, `video` (includes animated_gif), `mixed`, or `text_only`

## Caveats

- **Theme/sentiment classification is keyword-heuristic only.** It uses curated keyword
  lists with word-boundary matching, not NLP or ML. Results are approximate topic and
  tone signals. See `docs/analysis/data-contract.md` for the full taxonomy.
- **Engagement fields may be NULL** when upstream API data is incomplete. Views handle
  this with COALESCE for aggregation but preserve NULLs where meaningful (e.g., efficiency
  is NULL when views are unavailable).
- **Weekly rollups use ISO Monday week start.** The `week_start` column always falls on
  a Monday.

## Troubleshooting

### Network Access

All `ingest:*` and `fetch:*` commands require outbound HTTPS access to `api.twitterapi.io:443`.

Test connectivity:
```bash
curl -I https://api.twitterapi.io
```

If blocked by firewall/proxy, configure your environment to allow HTTPS egress.

### API Key and Credits

Verify your API key and account status:
```bash
# Should return user info, not 401/402/403
curl -H "X-API-Key: $TWITTERAPI_IO_KEY" \
  "https://api.twitterapi.io/twitter/user/info?userName=nasa"
```

Common errors:
- **HTTP 401 Unauthorized**: API key invalid or malformed
- **HTTP 402 Payment Required**: Account out of credits ([recharge at twitterapi.io](https://twitterapi.io))
- **HTTP 403 Forbidden**: API key disabled or account suspended
- **HTTP 429 Rate Limit**: Too many requests (default: 1000+ req/sec; wait or contact support)

### Sandbox Environments (Codex/Claude)

Codex and Claude sandboxes often have restricted outbound network access. If outbound HTTPS to
`api.twitterapi.io` is blocked, API commands will fail.

#### Workaround Strategy

1. **Ingest data on your local machine first** (where network access is unrestricted):
   ```bash
   # On your local machine
   backscraxer ingest:user --user-name nasa \
     --from 2025-01-01T00:00:00Z --to 2025-06-30T23:59:59Z \
     --db ~/data/twitter-analysis.db
   ```

2. **Copy the SQLite database into the sandbox environment**:
   - Codex: Place the `.db` file in your project directory before launching the agent
   - Claude: Upload the `.db` file as a project resource

3. **Use local-only commands in the sandbox**:
   ```bash
   # These work without network access
   backscraxer db:stats --db ./twitter-analysis.db
   backscraxer db:prune --before 2024-01-01T00:00:00Z --db ./twitter-analysis.db
   backscraxer report:users --db ./twitter-analysis.db --format csv
   backscraxer docs:get-endpoints

   # Query directly with SQLite
   sqlite3 ./twitter-analysis.db < src/db/views.sql
   sqlite3 -header -column ./twitter-analysis.db < examples/analysis_queries.sql
   ```

4. **All analysis happens locally** on the pre-ingested data

#### What Works in Sandboxes

✅ `docs:get-endpoints` — static metadata, no API call
✅ `db:stats` — read local database
✅ `db:prune` — modify local database
✅ `report:users` — query persisted DB data, no API call
✅ Direct SQLite queries on local `.db` files
✅ Applying analytics views (`src/db/views.sql`)
✅ Running analysis queries (`examples/analysis_queries.sql`)

❌ `fetch:user` — requires live API access
❌ `fetch:users` — requires live API access
❌ `ingest:user` — requires live API access
❌ `ingest:search` — requires live API access

### Offline/Local-Only Usage (General)

If network access to twitterapi.io is unavailable for any reason:
1. All `ingest:*` and `fetch:*` commands will fail (they require live API access)
2. Use **local-only commands** that work with already-ingested data (see above)
3. Query the SQLite database directly:
   ```bash
   sqlite3 ~/.backscraxer/data.db "SELECT COUNT(*) FROM tweets;"
   ```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | Internal error |
| 2 | Usage/flag validation error |
| 3 | Configuration/env error (e.g., missing API key) |
| 4 | Auth error (401/403) |
| 5 | Rate limit exhausted (429) |
| 6 | Network failure |
| 7 | Upstream API error (including 402 insufficient credits) |
| 8 | Database/filesystem error |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xsend) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
