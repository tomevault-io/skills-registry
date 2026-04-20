---
name: xmcp
description: Build, integrate, and troubleshoot xmcp-based MCP servers and adapters. Use when creating MCP tools/prompts/resources, bootstrapping with init-xmcp, wiring /mcp endpoints in Next.js/Express/NestJS, configuring auth/middleware, or debugging build/dev and adapter output. Use when this capability is needed.
metadata:
  author: olegakbarov
---

# XMCP

## Overview
Use this skill to stand up or extend an xmcp server quickly, including adapter wiring, tool discovery, auth middleware, and production build output. The full docs are in `references/xmcp-docs.md`.

## Workflow
1. Identify host framework (Next.js, Express, NestJS, or standalone).
2. Bootstrap with `npx init-xmcp@latest` and confirm tool/prompt/resource directories.
3. Wire the /mcp endpoint using the adapter output.
4. Add tools, prompts, resources with schemas and metadata.
5. Configure auth or middleware if needed.
6. Run `xmcp dev` and `xmcp build` and verify the `.xmcp` output is generated.
7. Debug path aliases and TypeScript config if the adapter import fails.

## Adapter decision guide
- Next.js: use the route handler and export GET/POST from the adapter.
- Express: import the handler from the adapter output and mount GET/POST.
- NestJS: use the generated module/controller and add the module to AppModule.

## Tool authoring checklist
- Export a schema and metadata in each tool file.
- Ensure tool filenames are in the tools directory so auto-discovery works.
- Keep schemas minimal and descriptive to help tool selection.

## Auth and middleware
- Prefer server-side auth and never expose secrets to the browser.
- For API key or JWT middleware, ensure headers are set by the MCP client.
- For OAuth providers, configure environment variables and middleware exports.

## Troubleshooting
- Missing `@xmcp/adapter` import: run `npx xmcp build` and add TS path aliases.
- Adapter output missing: confirm `xmcp dev` or `xmcp build` ran successfully.
- Requests failing: verify /mcp route registration and middleware ordering.

## References
- `references/xmcp-docs.md` contains the full documentation snapshot.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/olegakbarov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
