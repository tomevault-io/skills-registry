---
name: bitterblossom-dispatch
description: Operate the conductor dispatch loop and repair fleet readiness before work starts. Use when this capability is needed.
metadata:
  author: misty-step
---

# Bitterblossom Dispatch

Run this skill when you need to prepare the fleet and start the conductor loop. Direct ad hoc `bb dispatch` is no longer a supported surface.

## Preflight

```bash
source .env.bb
cd conductor
mix conductor check-env
mix conductor fleet --fleet ../fleet.toml --reconcile
```

Confirm:

- sprite auth is available through `SPRITE_TOKEN`, `FLY_API_TOKEN`, or `sprite auth login`.
- local verification tools are available (`git`, `sprite`, Dagger prerequisites from repo docs).
- declared sprites show healthy in `mix conductor fleet`.

## Workflow

1. Reconcile fleet readiness:

```bash
cd conductor
mix conductor fleet --fleet ../fleet.toml --reconcile
```

2. Start the control loop:

```bash
cd conductor
mix conductor start --fleet ../fleet.toml
```

3. Follow progress from another shell:

```bash
cd conductor
mix conductor logs <sprite> --follow
mix conductor show-runs --limit 10
```

## Failure Handling

- If fleet readiness fails, inspect `mix conductor fleet` output and fix auth/tooling gaps.
- If a sprite gets stuck, recover it from an Elixir shell with `Conductor.Sprite.kill/1`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/misty-step) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
