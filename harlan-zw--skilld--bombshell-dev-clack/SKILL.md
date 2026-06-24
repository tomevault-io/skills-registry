---
name: bombshell-dev-clack
description: ALWAYS use when writing code importing \"@clack/prompts\". Consult for debugging, best practices, or modifying @clack/prompts, clack/prompts, clack prompts, clack. Use when this capability is needed.
metadata:
  author: harlan-zw
---

# bombshell-dev/clack `@clack/prompts`

**Version:** 1.0.1 (yesterday)
**Deps:** picocolors@^1.0.0, sisteransi@^1.0.5, @clack/core@1.0.1
**Tags:** alpha: 1.0.0-alpha.10 (2 weeks ago), latest: 1.0.1 (yesterday)

**References:** [package.json](./.skilld/pkg/package.json) • [README](./.skilld/pkg/README.md) • [GitHub Issues](./.skilld/issues/_INDEX.md) • [Releases](./.skilld/releases/_INDEX.md)

## Search

Use `npx -y skilld search` instead of grepping `.skilld/` directories — hybrid semantic + keyword search across all indexed docs, issues, and releases.

```bash
npx -y skilld search "query" -p @clack/prompts
npx -y skilld search "issues:error handling" -p @clack/prompts
npx -y skilld search "releases:deprecated" -p @clack/prompts
```

Filters: `docs:`, `issues:`, `releases:` prefix narrows by source type.

## API Changes

⚠️ **ESM-only** — v1.0 dropped CJS dual-publish, `require('@clack/prompts')` no longer works [source](./.skilld/releases/@clack/prompts@1.0.0.md)

⚠️ `spinner.stop(msg, 1)` / `spinner.stop(msg, 2)` — v1.0 replaced numeric codes with `spinner.cancel(msg)` and `spinner.error(msg)` [source](./.skilld/releases/@clack/prompts@1.0.0.md)

⚠️ `suggestion` prompt — added then removed in v1.0, use `path` prompt (autocomplete-based) instead [source](./.skilld/releases/@clack/prompts@1.0.0.md)

⚠️ `placeholder` in `text()` — v1.0 changed to visual-only hint, no longer used as tabbable/return value [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `autocomplete()` / `autocompleteMultiselect()` — new in v1.0, searchable select with `filter` option for custom/fuzzy matching [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `progress()` — new in v1.0, displays a progress bar with `start()`, `stop()`, `cancel()`, `error()` methods [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `taskLog()` — new in v1.0, scrolling log output cleared on success; supports `group()` for nested log sections [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `box()` — new in v1.0, renders boxed text similar to `note` [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `path()` — new in v1.0, autocomplete-based file path prompt [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `stream.step()` — new in v0.10, renders async iterable message streams (useful for LLM output) [source](./.skilld/releases/@clack/prompts@0.10.0.md)

✨ `spinner({ indicator: 'timer' })` — new in v0.10, shows elapsed time instead of dots animation [source](./.skilld/releases/@clack/prompts@0.10.0.md)

✨ `updateSettings({ aliases, messages })` — new in v0.9, configures global keybindings and i18n cancel/error messages [source](./.skilld/releases/@clack/prompts@0.9.0.md)

✨ `signal` option — new in v0.9, all prompts accept `AbortSignal` for programmatic cancellation [source](./.skilld/releases/@clack/prompts@0.9.0.md)

✨ `withGuide` option — new in v1.0, disables the default clack border on any prompt [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `spinner.clear()` — new in v1.0, stops and clears spinner output entirely [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✨ `confirm({ vertical: true })` — new in v1.0.1, arranges yes/no options vertically [source](./.skilld/releases/@clack/prompts@1.0.1.md)

## Best Practices

✅ Use `spinner.cancel()` and `spinner.error()` instead of stop codes — v1.0 replaced `stop(msg, code)` with distinct methods [source](./.skilld/releases/@clack/prompts@1.0.0.md)

```ts
const s = spinner()
s.start('Deploying')
// s.stop('Done')       // success
// s.cancel('Aborted')  // user cancelled (CTRL+C)
// s.error('Failed')    // error occurred
// s.clear()            // stop and clear all output

```
✅ Pass `signal` to prompts for programmatic cancellation — all prompts accept `AbortSignal` since v0.9.0 [source](./.skilld/releases/@clack/prompts@0.9.0.md)

```ts
const answer = await confirm({
  message: 'Continue?',
  signal: AbortSignal.timeout(5000),
})
```

✅ Use `group()` with `onCancel` instead of checking `isCancel` after every prompt — centralizes cancellation handling for multi-step flows [source](./.skilld/pkg/README.md)

```ts
const result = await p.group({
  name: () => p.text({ message: 'Name?' }),
  lang: () => p.select({ message: 'Language?', options }),
}, {
  onCancel: () => { p.cancel('Cancelled.'); process.exit(0) },
})
```

✅ Use `updateSettings` for global i18n messages and key aliases — per-instance options override globals [source](./.skilld/releases/@clack/prompts@1.0.0.md)

```ts
import { updateSettings } from '@clack/prompts'
updateSettings({
  aliases: { w: 'up', s: 'down' },
  messages: { cancel: 'Cancelado', error: 'Error' },
})
```

✅ Use `stream` instead of `log` for LLM/async output — accepts sync and async iterables, renders incrementally [source](./.skilld/releases/@clack/prompts@0.10.0.md)

```ts
await stream.step((async function* () {
  yield* generateLLMResponse(question)
})())
```

✅ Use `taskLog` for subprocess output — renders lines continuously, clears on success, preserves on error [source](./.skilld/pkg/README.md)

```ts
const tl = taskLog({ title: 'Building' })
for await (const line of buildProcess()) tl.message(line)
success ? tl.success('Done') : tl.error('Failed')
```

✅ Distinguish `placeholder` from `defaultValue` in `text()` — placeholder is visual-only hint, never returned as value (changed in v1.0); use `defaultValue` for the fallback return value [source](./.skilld/issues/issue-321.md)

```ts
const name = await text({
  message: 'Project name?',
  placeholder: 'my-app',    // visual hint only, NOT returned
  defaultValue: 'my-app',   // returned when user presses Enter without typing
})
```

✅ v1.0 is ESM-only — CJS `require()` no longer works; use dynamic `import()` or switch to ESM [source](./.skilld/releases/@clack/prompts@1.0.0.md)

✅ Guard against empty `options` arrays in `select`/`multiselect` — passing `[]` throws `TypeError: Cannot read properties of undefined` [source](./.skilld/issues/issue-144.md)

✅ Vim keybindings (`h/j/k/l`) and `Escape` → cancel are enabled by default since v0.9.0 — `updateSettings` cannot disable defaults, only add aliases [source](./.skilld/releases/@clack/prompts@0.9.0.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/harlan-zw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
