---
name: context7-docs
description: Fetch up-to-date documentation for libraries and frameworks using Context7. Use this when the user asks for "latest docs", "API reference", or code examples for specific libraries. Use when this capability is needed.
metadata:
  author: spagestic
---

# Context7 Documentation Skill

This skill allows you to query the Context7 service to get the latest documentation for various libraries.

## Usage

To search for documentation, use the `mcporter` tool to bridge the CLI.

**Command Template:**

```bash
npx mcporter call --command "npx -y @upstash/context7-mcp" --tool <tool_name> --args <json_args>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spagestic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
