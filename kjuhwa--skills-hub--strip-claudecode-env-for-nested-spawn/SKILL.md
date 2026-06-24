---
name: strip-claudecode-env-for-nested-spawn
description: When spawning a Claude Agent SDK subprocess from within another Claude Code / Agent SDK context, the SDK's nesting guard will reject the child unless you strip CLAUDECODE from the inherited env. Use when this capability is needed.
metadata:
  author: kjuhwa
---

# Strip CLAUDECODE env before spawning Claude SDK subprocess

## When to use
- You're building tooling (CLI, dev harness, test runner) that sometimes runs inside a Claude Code / Agent SDK session and spawns a server that ALSO uses the Claude Agent SDK.
- The child process silently refuses to start, or the SDK throws an obscure "nesting detected" error.

## How it works
The Claude Agent SDK sets `CLAUDECODE=1` in its subprocess env. A nesting guard inside the SDK checks for this var and refuses to run to prevent infinite spawn loops. When you legitimately need to run an SDK subprocess from within an SDK session (e.g. `bun run scripts/server.ts` from Claude Code running a CLI), strip the var before spawn:

```ts
const { CLAUDECODE: _drop, ...parentEnv } = process.env;
spawn([...], { env: { ...parentEnv, /* your overrides */ } });
```

## Example
```ts
// apps/cli/src/server-spawner.ts
const { CLAUDECODE: _, ...parentEnv } = process.env;
const proc = Bun.spawn(['bun', 'run', serverEntry], {
  env: {
    ...parentEnv,
    CRAFT_SERVER_TOKEN: token,
    CRAFT_RPC_PORT: '0',
  },
  stdout: 'pipe',
  stderr: 'pipe',
});
```

## Gotchas
- You may also need to strip `CLAUDE_CODE_SSE_PORT`, `CLAUDE_CODE_OAUTH_TOKEN`, or other `CLAUDE_*` vars depending on SDK version - check SDK release notes.
- Don't blindly copy all env - the parent's `CRAFT_SERVER_URL` or debug flags may be inappropriate for the child.
- This is NOT a security-relevant strip (the guard is for loop prevention); the token is still the real auth boundary.
- After stripping, test both code paths: inside and outside a Claude session. Tests that only pass in one are a trap.
- Document why you're doing it - a comment saves the next maintainer an hour.

---
> Source: [kjuhwa/skills-hub](https://github.com/kjuhwa/skills-hub) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
