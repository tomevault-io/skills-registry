---
name: plans
description: >- Use when this capability is needed.
metadata:
  author: IndigoNakamoto
---

# Persist completed plan (`/plans`)

## When to run

- The user says **`/plans`**, **save the completed plan**, **archive the plan with results**, or similar after plan execution.
- All plan todos are **done** and the workspace should get a durable record under **[`.cursor/plans/`](../../plans/README.md)** (not only `~/.cursor/plans/`).

## Before writing

1. Read **[`.cursor/plans/README.md`](../../plans/README.md)** for naming, suggested sections, safety, and lifecycle.
2. Prefer the **completion date** (or authoritative session date) for `YYYY-MM-DD` in the filename.
3. If the plan text lives outside the repo, **read or reconstruct** it, then write the in-repo copy here.

## Filename

- Pattern: **`YYYY-MM-DD-short-slug.md`** (ASCII, lowercase slug, hyphen-separated).
- Optional: branch or PR id in the slug.
- On collision, use **`-2`** or a more specific slug; **do not** overwrite unrelated plans.

## Body template

Match the README’s suggested structure and add a **Results** block for what actually happened:

1. **Goal** — One short paragraph: what success meant.
2. **In scope / out of scope** — If the original plan had boundaries, preserve them (trim stale Cursor-only todo blocks).
3. **Primary files and canonical docs** — Paths and doc links the next reader needs.
4. **Execution results** — Concrete outcomes: what was run (high level), what succeeded or failed, important command/output snippets, fixes or workarounds (e.g. Docker network stale containers, CLI flag ordering), and **swap_ids / tx hashes / addresses only if they are dev/local and non-secret**; never paste private keys or credentialed URLs.
5. **Verification** — Commands or checks that proved the work (pointer to Makefile / mln-qa skill where appropriate).
6. **Layer-boundary check** (MLN) — One sentence: which of MWEB, LitVM, Nostr, Tor were touched and that boundaries were respected (see [`AGENTS.md`](../../../AGENTS.md)).

Optional short **Follow-ups** if something was explicitly deferred.

## Safety

- **No secrets**: no private keys, `.env` contents, or RPC URLs with credentials. Reference **`.example`** templates only (same as README).

## Lifecycle

- If this plan record accompanies merged work, **commit** the new `.cursor/plans/*.md` with that work per README lifecycle guidance.
- Align with **[`.cursor/rules/mln-plan-persistence.mdc`](../../rules/mln-plan-persistence.mdc)** (in-repo path, naming, stripping redundant Cursor frontmatter).

## Do not

- Edit unrelated existing plan files unless the user asked to supersede them (then a short **Superseded by** note at top is OK).
- Replace the README or the plan file the user said not to touch.

---
> Source: [IndigoNakamoto/mwixnet-litvm](https://github.com/IndigoNakamoto/mwixnet-litvm) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
