---
name: web-browse
description: Web browsing capability for agents to research and find information. Allows fetching web pages with JavaScript rendering, capturing screenshots, and extracting text content. All browsing is sandboxed and internal network access is blocked. Use when this capability is needed.
metadata:
  author: canivel
---

# Web Browse Skill

Enables agents to browse the web for research and information gathering. Pages are rendered with JavaScript support, and agents can capture screenshots or extract text content.

## Security

- **Sandboxed**: All browsing runs in isolated container environment
- **Internal network blocked**: Cannot access private IP ranges (10.x.x.x, 172.16-31.x.x, 192.168.x.x, localhost)
- **Timeouts**: 30 second maximum per page load
- **Audit logging**: All accessed URLs are logged for security review

## Usage

The skill provides three main functions accessible via the web-browse CLI:

### Fetch a page and extract text

```bash
web-browse fetch "https://example.com/docs" --output text
```

Returns the rendered text content of the page.

### Fetch a page and save HTML

```bash
web-browse fetch "https://example.com/docs" --output html
```

Returns the rendered HTML after JavaScript execution.

### Take a screenshot

```bash
web-browse screenshot "https://example.com/docs" --output screenshot.png
```

Saves a screenshot of the page. Use `--full-page` for full page capture.

### Advanced options

```bash
# Wait for specific selector before capturing
web-browse fetch "https://example.com" --wait-for ".content-loaded"

# Set viewport size
web-browse screenshot "https://example.com" --viewport 1920x1080

# Full page screenshot
web-browse screenshot "https://example.com" --full-page --output full.png
```

## Examples

### Research documentation

```bash
# Fetch and read TypeScript docs
web-browse fetch "https://www.typescriptlang.org/docs/handbook/intro.html" --output text
```

### Capture visual state

```bash
# Screenshot a dashboard
web-browse screenshot "https://example.com/dashboard" --viewport 1920x1080 --output dash.png
```

### Get rendered HTML for parsing

```bash
# Get HTML after JS execution
web-browse fetch "https://example.com/spa" --wait-for "#app-loaded" --output html
```

## Limitations

- Cannot access internal/private network addresses
- Maximum 30 second timeout per page
- Single page at a time (no parallel browsing)
- No persistent cookies or sessions between calls
- All activity is logged for audit

## Environment Variables

- `WEB_BROWSE_AUDIT_LOG`: Path to audit log file (default: `/tmp/web-browse-audit.log`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
