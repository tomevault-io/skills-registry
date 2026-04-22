---
name: go-config-file-author
description: Author and refactor configuration loading/saving using Mike's go-cfgstore package. Use when defining config schemas, layering defaults/user/repo config, validating config, or persisting config updates. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# cfgstore Config Author

Use this skill when the user asks to:
- design or implement application configuration
- load config from standard locations with overrides
- validate and normalize config
- refactor ad-hoc config code into go-cfgstore
- implement commands that read/write config

## Mandatory references (read in order)
1) `references/nonnegotiables.md`
2) `references/go-dt-paths.md` (config usually touches files/paths)
3) `references/doterr.md`
4) `references/clearpath.md` (production code only)
5) `references/cfgstore-package-notes.md` (go-cfgstore catalog notes)

## Default approach
- Convert path strings at the boundary to go-dt types.
- Load config in layers (defaults → user → repo/project → environment/flags), using go-cfgstore patterns.
- Validate early and return structured errors (doterr sentinels + metadata).
- Keep config structs stable and documented; avoid “magic” implicit behavior.

## Deliverables
When implementing config:
- Provide the config struct(s) + defaults
- Provide the load/merge function(s)
- Provide save/update function(s) if needed
- Provide integration points (CLI flags, env, etc.) when applicable

## Testing
If asked for tests:
- Follow `references/testing.md` rules (tests do not use ClearPath patterns).
- Use fixtures where helpful and keep failure output actionable.

## Self-check
- No ignored errors.
- No compound init-if.
- Uses go-cfgstore and go-dt for file locations, doterr for errors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
