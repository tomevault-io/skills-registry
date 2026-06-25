---
name: test-gaps
description: Detect missing test coverage and generate test suggestions Use when this capability is needed.
metadata:
  author: ruvnet
---
Find test coverage gaps via CLI:
```bash
npx @claude-flow/cli@latest hooks coverage-gaps --format table --limit 20
npx @claude-flow/cli@latest hooks coverage-route --task "add auth tests"
npx @claude-flow/cli@latest hooks coverage-suggest --path src/
```

Or dispatch the testgaps worker via MCP:
`mcp__claude-flow__hooks_worker-dispatch({ trigger: "testgaps" })`

For continuous detection, use `/loop` with the `loop-worker` skill targeting the `testgaps` worker.

---
> Source: [ruvnet/ruflo](https://github.com/ruvnet/ruflo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
