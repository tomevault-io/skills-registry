---
name: validate-changes
description: Use when working with a workflow to verify that code changes (refactoring, new features) didn't break the scraper or the build.
metadata:
  author: bartholomej
---

# Validate Changes

Since this project relies on scraping a 3rd party website (CSFD.cz), validation is crucial to ensure their DOM hasn't changed or our logic isn't broken.

## 1. Static Analysis & Build

Ensure that TypeScript compiles and the dual-build system works.

```bash
# Checks types and builds both 'server' and 'mcp-server' related outputs
yarn build
```

## 2. Unit & Integration Tests

Run the test suite. Note that some tests might hit the real CSFD website.

```bash
yarn test
```

## 3. Real-world Demo

The `demo.ts` file is a scratchpad for testing the actual scraper against current CSFD pages.

```bash
# Run the demo script
npm run demo
```

**If the demo fails:**

1. Check your internet connection (CSFD might block generic IPs).
2. Check if CSFD changed their DOM layout.
3. Verify `User-Agent` headers in `src/fetchers/fetcher.ts`.

## 4. MCP Server Check

If you modified `mcp-server/`, verify it builds and starts.

```bash
# Build specific to MCP
yarn build:mcp

# Try to run it (it handles stdio, so it will just wait for input, but shouldn't crash immediately)
node dist/bin/mcp-server.js
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bartholomej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
