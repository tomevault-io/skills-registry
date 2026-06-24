---
name: coding-conventions
description: Coding standards and conventions for the Aishipelago platform codebase. Use when this capability is needed.
metadata:
  author: emilcardell
---

# Coding Conventions

## When to use this skill

Use when:
- Writing new TypeScript code in any Aishipelago package
- Reviewing code for convention compliance
- Creating new CLI commands
- Writing or running tests
- Making Git commits

## Language and module system

- **TypeScript only**, strict mode enabled
- **ESM modules** — use `.js` extensions in all imports (even for `.ts` files)
- **Node16/NodeNext** module resolution
- **No default exports** — named exports only

```typescript
// Correct
import { registerFooCommand } from './commands/foo.js';
export { registerFooCommand };

// Wrong — no .js extension
import { registerFooCommand } from './commands/foo';

// Wrong — default export
export default function registerFooCommand() { ... }
```

## CLI patterns

- **Commander.js** for all CLI commands
- Follow the `registerXxxCommand(program)` pattern
- **Zod** for runtime schema validation
- **chalk** for terminal colors
- **ora** for spinners and progress indicators
- **No `process.exit()`** — set `process.exitCode = 1` instead

```typescript
import { Command } from 'commander';
import { z } from 'zod';
import chalk from 'chalk';
import ora from 'ora';

export function registerFooCommand(program: Command): void {
  program
    .command('foo <name>')
    .description('Do the foo thing')
    .action(async (name: string) => {
      const spinner = ora('Processing...').start();
      try {
        // ... work
        spinner.succeed(chalk.green('Done'));
      } catch (err) {
        spinner.fail(chalk.red('Failed'));
        process.exitCode = 1;
      }
    });
}
```

## Testing

- **Vitest** for all tests
- Run tests per package:

```bash
cd controller && npx vitest run --reporter verbose --config vitest.config.ts
cd mcp-server && npx vitest run --reporter verbose
cd cli && npx vitest run --reporter verbose
cd setup && npx vitest run --reporter verbose
```

## Git conventions

- **Conventional Commits** for all commit messages:
  - `feat:` — new feature
  - `fix:` — bug fix
  - `docs:` — documentation only
  - `infra:` — infrastructure/K8s manifests
  - `policy:` — Kyverno policies
  - `test:` — tests only
  - `chore:` — maintenance, dependencies

## Documentation

- Every feature gets a `docs/YYYY-MM-DD_featurename.md` file
- Update `docs/todo.md` when starting/completing features

## Project structure

```
aishipelago/
  app-template/       Template for new app repos
  backstage/          Developer portal (6 custom plugins)
  cli/                @aishipelago/cli (aish command)
  clusters/           Argo CD Application definitions
  controller/         AppRequest approval controller
  docs/               Feature docs (YYYY-MM-DD_name.md)
  docs-site/          Docusaurus documentation site
  examples/           Example apps (webshop monorepo)
  infrastructure/     K8s manifests (CRDs, Crossplane, Kyverno, etc.)
  mcp-server/         MCP AI tool interface
  scripts/            Bootstrap shell scripts
  setup/              @aishipelago/setup (platform setup tool)
```

## What NOT to do

- Never commit secrets, `.env` files, or API keys to Git
- Never commit `node_modules/` or `package-lock.json` to the platform repo
- Never write raw Kubernetes Secrets, ConfigMaps, or Deployments in app repos — use claims

---
> Source: [emilcardell/atlantis](https://github.com/emilcardell/atlantis) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
