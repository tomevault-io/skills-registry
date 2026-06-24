---
name: swpm-manage-commands-args
description: Manage cmds and args props in SWPM package manager configs (src/packages/managers/*.ts), including adding, modifying or removing command and argument mappings across npm, yarn, pnpm, bun, deno. Includes internal templates and real-world examples. Use when this capability is needed.
metadata:
  author: deinsoftware
---

# Manage SWPM Commands and Args

Use this skill when creating or modifying `cmds` and/or `args` props in package manager configuration files under `src/packages/managers/*.ts`. Handles adding new mappings, updating existing ones, and ensuring consistency across all supported package managers.

## When to Use

- User requests to add/modify/remove a command or argument mapping
- Changes to `cmds` or `args` props in `src/packages/managers/*.ts` files
- Ensuring consistent command/argument support across npm, yarn, pnpm, bun, deno
- **Do not use** for changes outside manager config files, or for commands/args identical across all package managers

## Instructions

1. **Modify/Add cmds or args in package manager configs**
   - Edit target files in `src/packages/managers/*.ts` (npm.ts, yarn.ts, pnpm.ts, bun.ts, deno.ts)
   - For `cmds` prop: follow templates in `templates/commands.md` (Replace, Replace+Flag, Not Available, Positional)
   - For `args` prop: follow templates in `templates/args.md` (Replace, Replace with Command, Convert to Commands, Remove, Package Decoration, Not Available)
   - Ensure consistency: update all relevant manager files for the same command/argument

2. **Handle command files (only for new/modified commands)**
   - New command: Create `src/cli/swpm/commands/<command-name>.ts` with @yargs v18 API
   - Modified command: Edit existing command file if behavior changes
   - Command file MUST exist for the command to work

3. **Update CLI registration (only for new commands)**
   - Edit `src/cli/swpm/cli.ts` to register new commands
   - Verify existing registration is valid for modified commands

4. **Update documentation**
   - Update `CHEATSHEET.md` with `[x]`/`[ ]` markers for command support
   - Update `README.md` if support status changed (remove/add availability warnings)

5. **Verify**
   - Run `npm run build` to compile TypeScript
   - Run `npm run test` to check regressions
   - Test changes with target package managers

## Constraints

- New commands require all three: manager config + command file + cli.ts registration
- Args-only changes only require manager config updates
- Only map `cmds`/`args` that differ across package managers
- Identical commands/args across all managers do NOT need mapping
- SWPM passes through unknown commands/args as-is to detected package manager
- ESM only, Node.js >= 20, TypeScript strict mode
- @yargs v18 API (not v17 or earlier)

## Examples

### Example 1: Replace command (remove → uninstall)

**User request**: "Map `remove` command to `uninstall` for npm"

**Context**: npm uses `uninstall` while other package managers use `remove`

**Action**:

1. Edit `src/packages/managers/npm.ts` `cmds` prop following `templates/commands.md`:

```typescript
cmds: {
  remove: 'uninstall',
  r: 'uninstall',
}
```

**Result**: `swpm remove express` → `npm uninstall express`

---

### Example 2: Replace+Flag command (upgrade → install --latest)

**User request**: "Make `upgrade` command use `install --latest` in npm"

**Context**: npm uses `install --latest` to upgrade packages, while pnpm uses `update`

**Action**:

Edit `src/packages/managers/npm.ts` `cmds` prop:

```typescript
cmds: {
  upgrade: ['install', '--latest'],
  ug: ['install', '--latest'],
}
```

**Result**: `swpm upgrade express` → `npm install express --latest`

---

### Example 3: Not Available command

**User request**: "Mark `why` command as not available for npm"

**Context**: npm doesn't have a `why` command, but pnpm and yarn do

**Action**:

Edit `src/packages/managers/npm.ts` `cmds` prop:

```typescript
cmds: {
  why: ['', -1],
}
```

**Result**: `swpm why express` → Not executed in npm (shows error/warning)

---

### Example 4: Positional command (run with -- separator)

**User request**: "Add support for passing args after `--` in `run` command"

**Context**: Need to pass additional args to the script being run

**Action**:

Edit all manager config files:

```typescript
cmds: {
  run: { '--': '--' },
}
```

**Result**: `swpm run test -- --watch` → `npm run test -- --watch`

---

### Example 5: Package Decoration arg (--latest → @latest)

**User request**: "Make `--latest` flag add `@latest` to package name"

**Context**: Install package with latest tag

**Action**:

Edit `src/packages/managers/npm.ts` `args` prop following `templates/args.md`:

```typescript
args: {
  '--latest': ['<package>@latest', 1],
  '-L': ['<package>@latest', 1],
}
```

**Result**: `swpm add express --latest` → `npm install express@latest`

---

### Example 6: Replace with Command arg (--global → global command)

**User request**: "Convert `--global` flag to `global` command for npm"

**Context**: npm uses `--global` flag, pnpm uses `global` subcommand

**Action**:

Edit `src/packages/managers/pnpm.ts` `args` prop:

```typescript
args: {
  '--global': ['global', 1],
  '-g': ['global', 1],
}
```

**Result**: `swpm add express --global` → `pnpm global add express`

---

### Example 7: Convert to Commands arg (--frozen → ci)

**User request**: "Make `--frozen` flag convert `install` to `ci` command"

**Context**: pnpm uses `ci` for frozen installs

**Action**:

Edit `src/packages/managers/pnpm.ts` `args` prop:

```typescript
args: {
  '--frozen': {
    install: 'ci',
    i: 'ci',
  },
}
```

**Result**: `swpm install --frozen` → `pnpm ci`

---

## Observations

Before acting, observe:

- Review existing `cmds` and `args` patterns in manager files at `src/packages/managers/*.ts`
- Verify `CHEATSHEET.md` for current implementation status
- Check `src/cli/swpm/commands/` for existing command files
- Reference `templates/commands.md` and `templates/args.md` for configuration patterns

---
> Source: [deinsoftware/swpm](https://github.com/deinsoftware/swpm) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
