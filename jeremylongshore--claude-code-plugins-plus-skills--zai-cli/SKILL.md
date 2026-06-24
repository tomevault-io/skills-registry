---
name: zai-cli
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Zai Cli

## Overview

ZAI CLI provides access to Z.AI capabilities including image/video analysis, real-time web search, web page extraction, and GitHub code exploration. It integrates with Claude Code via MCP protocol for seamless AI-powered content analysis.

## Prerequisites

- Node.js 18+ installed
- Z_AI_API_KEY environment variable set
- API key from https://z.ai/manage-apikey/apikey-list
- Network access to Z.AI API endpoints

## Instructions

1. Obtain an API key from Z.AI platform
2. Export your API key: `export Z_AI_API_KEY="your-key"`
3. Run `npx zai-cli doctor` to verify setup
4. Use `npx zai-cli --help` to see available commands
5. Try basic commands like vision, search, read, or repo
6. Use `--help` on any subcommand for detailed options

Access Z.AI capabilities via `npx zai-cli`. The CLI is self-documenting - use `--help` at any level.

## Output

Default: **data-only** (raw output for token efficiency).
Use `--output-format json` for `{ success, data, timestamp }` wrapping.

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- [Z.AI Platform](https://z.ai/)
- [Z.AI API Key Management](https://z.ai/manage-apikey/apikey-list)
- [zai-cli npm Package](https://www.npmjs.com/package/zai-cli)
- [Z.AI Documentation](https://docs.z.ai/)
- [MCP Protocol Reference](https://modelcontextprotocol.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
