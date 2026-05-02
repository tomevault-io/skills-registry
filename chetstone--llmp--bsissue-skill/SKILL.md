---
name: bsissue-skill
description: Extract and format Bugsnag error event data, post it to GitHub issues. Use when working with Bugsnag errors that need to be documented, analyzed, or tracked in GitHub. Use when this capability is needed.
metadata:
  author: chetstone
---

# Bugsnag Issue Summarizer Skill

This skill enables you to fetch Bugsnag error events, format them as comprehensive Markdown reports, and post them to GitHub issues.

## When to Use This Skill

Use this skill when you need to:
- Analyze a Bugsnag error event
- Document a Bugsnag error in a GitHub issue
- Get detailed information about an error (stacktrace, device info, breadcrumbs, metadata)
- Compare multiple Bugsnag errors
- Track error investigations in GitHub

## CRITICAL: Agent vs Human Mode

**When running as an AI agent (you!), ALWAYS use the `--no-summarize` flag** to get complete, untruncated data:
```bash
bsissue "URL" --no-summarize
```

Without this flag, Timer/Counter metadata is truncated to top 5 entries and breadcrumbs limited to 20, which may hide critical information for your analysis.

**Only omit `--no-summarize` when:**
- Posting to GitHub issues (humans prefer summarized view)
- User explicitly asks for a summary
- Generating human-readable output

## Prerequisites

**Authentication Required:**
- `BUGSNAG_AUTH_TOKEN` - Bugsnag Data Access API token
  - Set as environment variable OR
  - Configure in `~/.codex/config.toml` under `[mcp_servers.smartbear].env.BUGSNAG_AUTH_TOKEN`
- `GITHUB_TOKEN` - GitHub personal access token (only for posting to issues)

**Installation:**
The `bsissue` CLI tool must be installed:
```bash
uv tool install /home/user/llmp
# or
pip install /home/user/llmp
```

## Available Commands

### 1. Fetch and Display Bugsnag Event

**Command:**
```bash
bsissue "BUGSNAG_URL" [--no-summarize] [--format FORMAT]
```

**When:** You need to view or analyze a Bugsnag error event.

**Input:** Bugsnag dashboard URL containing `?event_id=...`
- Example: `https://app.bugsnag.com/org/project/errors/abc?event_id=123456`

**Output:** Formatted Markdown with:
- Error summary (class, message, release stage, device, context)
- Device information table
- Breadcrumbs (with relative timestamps)
- Stacktrace (code frames highlighted, full trace collapsible)
- App information table
- Metadata (Timer, auth, counter, multi sections)
- Feature flags and correlation data

**Options:**
- `--no-summarize` - **IMPORTANT FOR AGENTS**: Disables truncation of breadcrumbs, Timer, and Counter metadata. Use this flag when you need complete data for analysis.
- `--format json` - Output raw event JSON instead of formatted Markdown

**Example (Human use):**
```bash
bsissue "https://app.bugsnag.com/myorg/myapp/errors/error123?event_id=abc123"
```

**Example (Agent use - get full data):**
```bash
bsissue "https://app.bugsnag.com/myorg/myapp/errors/error123?event_id=abc123" --no-summarize
```

**Example (Get raw JSON):**
```bash
bsissue "https://app.bugsnag.com/myorg/myapp/errors/error123?event_id=abc123" --format json
```

**By Default (Human-Readable Mode):**
- Breadcrumbs: First 20 shown, rest collapsed
- Timer metadata: Top 5 sessions/sessionStatus
- Counter metadata: Top 5 counts/rounds

**With --no-summarize (Agent Mode):**
- ALL breadcrumbs shown
- FULL Timer metadata (no truncation)
- FULL Counter metadata (no truncation)

### 2. Post Bugsnag Event to GitHub Issue

**Command:**
```bash
bsissue "BUGSNAG_URL" --post "GITHUB_ISSUE_SPEC"
```

**When:** You want to document a Bugsnag error in a GitHub issue.

**GitHub Issue Spec Formats:**

| Format | Example | Behavior |
|--------|---------|----------|
| `owner/repo#123` | `"myorg/myapp#45"` | Post to issue #45 in myorg/myapp |
| `repo#123` | `"myapp#45"` | Post to issue #45 (owner from git remote if available) |
| `123` | `"45"` | Post to issue #45 in current git repository |
| `owner/repo` | `"myorg/myapp"` | Create new issue in myorg/myapp |
| `repo` | `"myapp"` | Create new issue (owner from git remote if available) |

**Smart Owner/Repo Resolution:**
- Bare issue number (e.g., `"45"`): Extracts owner/repo from git remote origin URL. **Fails if not in a git repository.**
- Missing owner (e.g., `"myapp#45"`): Uses git remote if available, otherwise defaults to "chetstone"
- Full spec (e.g., `"myorg/myapp#45"`): Uses exactly what you specify

**Examples:**
```bash
# In a git repo with remote git@github.com:myorg/myapp.git

# All post to myorg/myapp#45:
bsissue "URL" --post "45"
bsissue "URL" --post "myapp#45"
bsissue "URL" --post "myorg/myapp#45"

# Create new issue:
bsissue "URL" --post "myorg/myapp"

# Post to different repo:
bsissue "URL" --post "otherorg/otherapp#123"
```

**Behavior:**
- Automatically splits large comments across multiple GitHub comments
- Preserves collapsible sections for readability
- Handles authentication via `GITHUB_TOKEN` environment variable
- When creating new issue: title extracted from error, details in comments

### 3. Override Project ID

**Command:**
```bash
bsissue "BUGSNAG_URL" --project-id "PROJECT_ID"
```

**When:** Working with multiple Bugsnag projects.

**Default:** `5e7e6984e2a5fe0014dbf1a8`

**Example:**
```bash
bsissue "https://app.bugsnag.com/.../errors/...?event_id=abc" --project-id "custom-project-id"
```

### 4. Use Local JSON File (Testing)

**Command:**
```bash
bsissue "/path/to/event.json"
```

**When:** Testing with saved event data or working offline.

**Example:**
```bash
bsissue "tests/fixtures/bugsnag_event_full.json"
```

## Workflow Examples

### Example 1: Analyzing an Error

**User Request:** "Analyze this Bugsnag error: https://app.bugsnag.com/org/app/errors/x?event_id=123"

**Your Actions:**
1. Run: `bsissue "https://app.bugsnag.com/org/app/errors/x?event_id=123" --no-summarize`
2. Review the FULL output focusing on:
   - Error class and message
   - Stacktrace (especially in_project frames)
   - ALL breadcrumbs leading to error (not just first 20)
   - Device and app context
   - COMPLETE Timer/Counter metadata (may reveal patterns)
3. Provide analysis based on the complete data

### Example 2: Documenting in GitHub

**User Request:** "Post this error to GitHub issue #45"

**Your Actions:**
1. Get the Bugsnag URL from user or context
2. Run: `bsissue "BUGSNAG_URL" --post "45"`
   - NOTE: Do NOT use --no-summarize when posting to GitHub
   - GitHub comments are for humans who prefer summarized, readable format
3. Confirm successful posting

### Example 3: Comparing Multiple Errors

**User Request:** "Are these two errors related?"

**Your Actions:**
1. Run `bsissue "URL1" --no-summarize` and `bsissue "URL2" --no-summarize`
2. Compare COMPLETE data:
   - Error classes and messages
   - Stacktraces (look for common frames)
   - Release versions
   - ALL breadcrumbs (may show different user paths)
   - FULL Timer/Counter metadata (may reveal timing patterns)
3. Provide comparison summary highlighting similarities and differences

### Example 4: Tracking an Investigation

**User Request:** "I'm investigating error abc123, track my progress"

**Your Actions:**
1. Fetch the error: `bsissue "BUGSNAG_URL"`
2. Post to new GitHub issue: `bsissue "BUGSNAG_URL" --post "owner/repo"`
3. Use the created issue number for ongoing updates
4. Add analysis comments to the issue as investigation progresses

## Output Format Details

The Markdown output includes these sections:

### 1. Summary
- Error class and message
- Release stage and version
- Device platform/model/OS
- User note (if present)
- Context, unhandled status, severity
- User information (id, name, email)

### 2. Device Table
Merged data from top-level `device` and `metaData.device` (metadata wins on conflicts)

### 3. Breadcrumbs
- Sorted by timestamp (most recent first)
- Relative timestamps ("2.5s before", "3m 15s before")
- First 20 shown inline, rest in collapsible section
- Formatted from metadata arrays

### 4. Stacktrace
- **If code frames exist:** Highlighted code frames at top, full trace collapsible
- **If no code frames:** Entire trace in collapsible section
- Shows: file path, line number, method, in_project marker

### 5. App Table
Merged data from top-level `app` and `metaData.app`

### 6. Metadata
Collapsible sections for:
- Timer (sessions and sessionStatus, top 5 entries if large)
- auth
- counter (counts and rounds, top 5 entries if large)
- multi

### 7. Feature Flags & Correlation
Lists feature flags and correlation data

## Error Handling

**Authentication Errors:**
```
Error: BUGSNAG_AUTH_TOKEN not found in env or config
```
→ Set token in environment or `~/.codex/config.toml`

**Invalid URL:**
```
Error: Could not find event_id in URL
```
→ Ensure URL contains `?event_id=...`

**GitHub Posting Errors:**
```
Error posting to GitHub: GITHUB_TOKEN not set
```
→ Set `GITHUB_TOKEN` environment variable

**Network/API Errors:**
```
Error fetching event: <details>
```
→ Check network, authentication, and event ID validity

## Best Practices

1. **ALWAYS use `--no-summarize` for analysis** - As an AI agent, you need complete data to make informed decisions. Only omit this flag when posting to GitHub.
2. **Always get user confirmation** before posting to GitHub issues
3. **Provide context** when analyzing errors (not just raw output)
4. **Highlight critical information** from the formatted output:
   - Root cause indicators in stacktrace
   - Relevant breadcrumbs showing user actions (check ALL of them with --no-summarize)
   - Important metadata values (Timer/Counter patterns may be in entries beyond top 5)
5. **Use relative issue numbers** when working within a git repository (`--post "123"`)
6. **Save large events locally** for repeated analysis to avoid API rate limits
7. **Consider using `--format json`** if you need programmatic access to raw event structure

## Tips

- The tool automatically handles oversized GitHub comments by splitting them
- Breadcrumbs are sorted newest-first for easier analysis
- Timer and counter metadata are automatically summarized if large
- Code frames show actual source code when available in Bugsnag
- The `in_project` marker helps identify application code vs. library code

## Advanced Usage

### Programmatic Access in Python

```python
from bsissue.cli import _load_event_content, _build_markdown

# Load event
event = _load_event_content("https://app.bugsnag.com/...?event_id=123", "project-id")

# Generate markdown
markdown = _build_markdown(event)

# Or get structured sections
from bsissue.cli import _build_sections
sections = _build_sections(event)
for section_name, section_content in sections:
    print(f"=== {section_name} ===")
    print(section_content)
```

### Offline Testing

Generate sample output without API access:
```bash
python - <<'PY'
import json
from bsissue.cli import _build_markdown
with open('tests/fixtures/bugsnag_event_full.json') as f:
    print(_build_markdown(json.load(f)))
PY
```

## Reference Files

- **examples/sample-output.md** - Example of formatted Bugsnag event
- **examples/github-issue-template.md** - Template for manual issue creation
- **scripts/batch-fetch.sh** - Script for fetching multiple events

## Related Tools

- **Bugsnag Dashboard** - https://app.bugsnag.com
- **Bugsnag API Docs** - https://bugsnag.com/docs/api/
- **GitHub CLI** - Use `gh` for additional GitHub operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chetstone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
