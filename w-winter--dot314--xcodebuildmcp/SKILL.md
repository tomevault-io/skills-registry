---
name: xcodebuildmcp
description: Build/test Xcode projects via the XcodeBuildMCP MCP server using a local CLI wrapper for pi (no MCP support). Use when the user mentions Xcode, xcodebuild, iOS builds/tests, or XcodeBuildMCP. Use when this capability is needed.
metadata:
  author: w-winter
---

# xcodebuildmcp

This skill wraps an MCP server behind a local CLI so pi agent can use it without MCP support.

## Setup

Run once:

```bash
cd {baseDir}
npm install
```

## Usage

Use `--output json` for agent workflows.

```bash
{baseDir}/xcodebuildmcp.sh --help
{baseDir}/xcodebuildmcp.sh --output json <command> [flags]
```

### Regenerating the CLI

If the MCP server adds/removes tools, regenerate the CLI from its embedded metadata:

```bash
cd {baseDir}
npx mcporter generate-cli --from ./xcodebuildmcp.ts
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/w-winter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
