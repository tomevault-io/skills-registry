---
name: browser-fetch
description: Fetch authenticated web content using browser session (SSO, internal sites) Use when this capability is needed.
metadata:
  author: benthomasson
---

# browser-fetch Skill

Fetch web content from authenticated/internal sites using a real browser with persistent login sessions.

**Repository:** https://github.com/benthomasson/browser-fetch

## When to Use This Skill

Use this skill when:
- Fetching internal company pages that require SSO login
- curl returns login pages or 401/403 errors
- WebFetch fails due to authentication
- You need to access content behind Red Hat SSO or similar

## Quick Start (Server Mode - Recommended)

```bash
# User starts server (token required by default):
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --serve https://internal.redhat.com

# Server prints a token. User provides token to Claude. Claude fetches via curl:
curl 'http://localhost:8080/fetch?token=TOKEN&url=https://internal.redhat.com/page&text=true'
```

**IMPORTANT:** Ask the user for the token printed by the server.

## Alternative: Single-Fetch Mode

```bash
# First time: Login to save session (opens browser)
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --login https://internal.redhat.com

# After login: Fetch headlessly
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --headless --text https://internal.redhat.com/page
```

## Common Commands

### Login (First Time)

```bash
# Open browser for manual SSO login
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --login https://source.redhat.com

# Log in via browser, then press Enter
# Session saved to ~/.config/browser-fetch/profile
```

### Fetch Content

```bash
# Fetch full HTML
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --headless https://internal.redhat.com/page

# Fetch text only (no HTML tags) - easier to read
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --headless --text https://internal.redhat.com/page

# Extract specific element by CSS selector
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --headless --selector "main" --text https://docs.example.com/page

# Save to file
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --headless --text -o /tmp/page.txt https://internal.redhat.com/page
```

## Options

| Option | Description |
|--------|-------------|
| `--login` | Open browser for manual login, save session |
| `--headless` | Run without visible browser (requires saved session) |
| `--text` | Extract text content only (no HTML) |
| `--selector CSS` | Extract specific element by CSS selector |
| `-o FILE` | Save output to file |
| `--wait N` | Wait N seconds after page load (default: 5) |
| `--timeout N` | Page load timeout in seconds (default: 30) |
| `--profile-dir DIR` | Custom profile directory |
| `--serve` | Run as HTTP server (browser stays open) |
| `--port N` | Server port (default: 8080) |
| `--no-token-dangerous` | Disable security token (NOT recommended) |

## Server Mode (Recommended for SSO)

User starts server in a separate terminal:
```bash
uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --serve https://source.redhat.com
```

Server prints a security token. **Ask user to provide this token.**

Claude fetches via curl (with token):
```bash
# Fetch text content
curl 'http://localhost:8080/fetch?token=TOKEN&url=https://source.redhat.com/page&text=true'

# Fetch with selector
curl 'http://localhost:8080/fetch?token=TOKEN&url=https://source.redhat.com/page&selector=main&text=true'

# Check if server is running (no token needed)
curl 'http://localhost:8080/health'
```

**Query parameters:**
- `token` - security token (required by default)
- `url` - URL to fetch (required)
- `text=true` - extract text only
- `selector=CSS` - extract specific element
- `wait=N` - wait N seconds after load

## Workflow Example (Server Mode)

When user asks to read an internal page:

1. **Check if server is running**:
   ```bash
   curl -s 'http://localhost:8080/health' || echo "Server not running"
   ```

2. **If not running, ask user to start it and provide token**:
   ```
   Please start browser-fetch server and give me the token:
   uvx --from "git+https://github.com/benthomasson/browser-fetch" browser-fetch --serve https://source.redhat.com
   ```

3. **If running but you get 401, ask user for the token**:
   ```
   I got 401 Unauthorized. Please provide the security token from the browser-fetch server output.
   ```

4. **Fetch content via curl (with token)**:
   ```bash
   curl -s 'http://localhost:8080/fetch?token=USER_PROVIDED_TOKEN&url=https://source.redhat.com/page&text=true'
   ```

5. **Or save to file and read**:
   ```bash
   curl -s 'http://localhost:8080/fetch?token=TOKEN&url=https://source.redhat.com/page&text=true' > /tmp/page.txt
   ```
   Then use the Read tool to read `/tmp/page.txt`

## Tips

1. **Token is required by default** - Prevents other processes from using your session
2. **Ask user for token** - Token is printed when server starts
3. **Server mode for SSO** - Keeps browser open, handles auth better
4. **Use `text=true`** for readable output without HTML clutter
5. **Check health first** - `curl localhost:8080/health` to verify server is up (no token needed)

## Comparison with Other Tools

| Tool | Use Case |
|------|----------|
| curl | Public pages, APIs with tokens |
| WebFetch | Public pages (can be slow/unreliable) |
| browser-fetch | Authenticated pages, SSO, internal sites |

## Troubleshooting

**"Session expired"** - Re-run with `--login` to refresh

**"Playwright not installed"** - First time setup:
```bash
cd ~/git/browser-fetch && uv sync && uv run playwright install chromium
```

**Page loads but content missing** - Increase wait time:
```bash
browser-fetch --headless --wait 10 --text URL
```

**SSO redirect loops** - Some SSO systems need manual login each time

---

*Last updated: 2026-02-10*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/benthomasson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
