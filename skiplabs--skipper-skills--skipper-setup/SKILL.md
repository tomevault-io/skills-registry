---
name: skipper-setup
description: >- Use when this capability is needed.
metadata:
  author: SkipLabs
---

# Skipper setup & onboarding

Get a machine ready to use Skipper. A couple of steps are automatable; the ones
that matter most — account, login, payment — need a human at a browser. Your
job is to **detect what's missing and guide**, never to fake them.

## What you can and cannot do

| Step    | You can…                              | You cannot…                    |
| ------- | ------------------------------------- | ------------------------------ |
| CLI     | run via `npx --yes @skiplabs/skipper` | —                              |
| Docker  | start it on macOS; poll until ready   | install Docker                 |
| Account | give the sign-up link                 | create the account             |
| Login   | give the command; user runs it        | log in (browser + token paste) |
| Balance | give the top-up link                  | add funds                      |

## Step 1 — The CLI command

Always invoke the CLI as **`npx --yes @skiplabs/skipper`**. Don't use a
`skipper` on `PATH` — it may be a different tool or a local dev build pointing
at the wrong backend. Below, `skipper <cmd>` means
`npx --yes @skiplabs/skipper <cmd>`.

## Step 2 — See what's missing

```bash
skipper status --json
```

Read `.docker`, `.auth.token`, `.balance.total`. Fix each failing one **in
order** (Docker → login → balance), re-running `status --json` after each so you
work from fresh state.

## Step 3 — Docker

If `.docker` is not `true`:

- **macOS** — offer to start Docker Desktop and wait for it to come up:
  ```bash
  open -a Docker
  for i in $(seq 1 30); do docker info >/dev/null 2>&1 && break; sleep 2; done
  docker info >/dev/null 2>&1 && echo "docker ready" || echo "still down"
  ```
- **Linux / Windows / not installed** — tell the user to start the Docker
  daemon, or install Docker first (<https://docs.docker.com/get-docker/>). You
  cannot install it for them.

## Step 4 — Account & login

If `.auth.token` is not `"valid"`:

1. No account yet? Point them to sign up: <https://www.skipper.skiplabs.io>.
2. Have **them** run login **in their own terminal** — it opens a browser and
   reads a pasted token from an interactive prompt:
   ```bash
   npx --yes @skiplabs/skipper login
   ```
   **Do not run this with the `!` inline prefix** (and don't run it yourself).
   The token prompt needs a real interactive terminal; under Claude Code's `!`
   shell stdin isn't a proper TTY, so the prompt mis-reads the paste and the
   CLI crashes with an "unsettled top-level await". A normal terminal works.
3. Ask the user to tell you when login finishes, then re-run
   `skipper status --json` and confirm `.auth.token == "valid"`.

**Never invent, guess, or hard-code a token, and never read it from the user's
terminal output to store it yourself — the login flow is the only safe path.**

## Step 5 — Balance

If `.balance.total` is `0` or `null`:

- Tell the user to top up at <https://www.skipper.skiplabs.io>.
- Generation costs money: `auto` hard-gates at ≥ $10; `create` only needs
  `> 0`, but a non-trivial service will use more.
- Re-check with `skipper status --json` (or `skipper balance`).

## Done

When `.docker == true`, `.auth.token == "valid"`, and `.balance.total > 0`, the
environment is ready. If the user came here to build something, hand control
back to the `/skipper:skipper-build` skill to author the `prompt.md`, generate,
and run.

---
> Source: [SkipLabs/skipper-skills](https://github.com/SkipLabs/skipper-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
