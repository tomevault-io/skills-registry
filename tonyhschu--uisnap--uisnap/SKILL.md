---
name: uisnap
description: Captures browser state to disk for token-efficient debugging. Use for frontend debugging, performance analysis, accessibility testing, or when investigating page behavior. Supports snapshots (functional debugging) and Chrome traces (performance profiling). Use when this capability is needed.
metadata:
  author: tonyhschu
---

# uisnap

Disk-based debugging for Claude Code. **Capture once, query many.**

## Quick Decision Tree

**User reports functional issues** → Snapshot capture
- "Button doesn't work"
- "Form submission fails"
- "Console errors"
- "API returns 404"
- "Page doesn't load"

→ See [snapshot-capture-and-analysis.md](snapshot-capture-and-analysis.md)

**User reports performance issues** → Trace capture
- "Page is slow/laggy"
- "Scrolling is janky"
- "Animations stutter"
- "UI freezes"
- "Long blocking tasks"

→ See [trace-capture-and-analysis.md](trace-capture-and-analysis.md)

**Not sure?** → Start with snapshot (faster, cheaper), escalate to trace if needed

## Common Commands

### Snapshot Workflow
```bash
# Capture
uisnap snapshot https://myapp.com

# Analyze
uisnap analyze-console snapshots/myapp.com/latest/console.jsonl
uisnap analyze-network snapshots/myapp.com/latest/network.jsonl
uisnap query-a11y snapshots/myapp.com/latest/a11y.yaml "Submit"
```

### Trace Workflow
```bash
# Capture
uisnap exec examples/scroll-perf-trace.js https://myapp.com

# Analyze
uisnap trace-analyze snapshots/myapp.com/latest/chrome-trace.db --full
```

## Available Tools

**Capture commands**:
- `uisnap snapshot <url>` - Capture page state (a11y, console, network)
- `uisnap exec <script.js> <url>` - Run custom script with Playwright context

**Analysis commands**:
- `uisnap analyze-console <console.jsonl>` - Summarize console messages
- `uisnap analyze-network <network.jsonl>` - Summarize network activity
- `uisnap query-a11y <a11y.yaml> <query>` - Query accessibility tree
- `uisnap trace-analyze <trace.db>` - Analyze performance trace

## Installation

```bash
npm install -g uisnap
npx playwright install chromium
```

## When to Read Reference Docs

**For snapshot debugging**: See [snapshot-capture-and-analysis.md](snapshot-capture-and-analysis.md)
- Complete capture and analysis guide
- Multi-step flows with `utils.captureStep()`
- Output formats and querying patterns
- Examples and troubleshooting

**For performance debugging**: See [trace-capture-and-analysis.md](trace-capture-and-analysis.md)
- Chrome tracing setup and workflow
- DuckDB schema and canned queries
- Custom SQL for advanced analysis
- Examples and troubleshooting

## Progressive Workflow

1. **Start with snapshot** - Check for functional errors first
2. **If no errors but issues persist** - Capture trace for performance analysis
3. **For comprehensive debugging** - Combine both approaches

Example combining both:
```javascript
// Capture functional state
await utils.captureStep(page, 'initial-load', async () => {
  await page.goto('https://myapp.com');
});

// Start performance tracing
const cdp = await context.newCDPSession(page);
await utils.startChromeTrace(cdp);

// Perform interactions (traced)
await page.evaluate(() => window.scrollTo({ top: 1000 }));

// Capture final state
await utils.captureStep(page, 'after-scroll', async () => {});

// Stop trace and auto-import
const tracePath = path.join(utils.baseOutputDir, 'chrome-trace.json');
await utils.stopChromeTraceAndImport(cdp, tracePath);
```

## Output Structure

Auto-generated timestamped directories:
```
snapshots/
├── myapp.com/
│   ├── 2025-12-30-120000/
│   │   ├── a11y.yaml
│   │   ├── console.jsonl
│   │   ├── network.jsonl
│   │   └── metadata.json
│   ├── 2025-12-30-130000/
│   └── latest -> 2025-12-30-130000/
```

Use `latest` symlink for most recent capture.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tonyhschu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
