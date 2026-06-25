---
name: scaffold-worker
description: Initializes the Tauri 2 project with React, TailwindCSS, shadcn/ui, and i18n from scratch Use when this capability is needed.
metadata:
  author: iamzhihuix
---

# Scaffold Worker

NOTE: Startup and cleanup are handled by `worker-base`. This skill defines the WORK PROCEDURE.

## When to Use This Skill

For features that initialize or significantly restructure the project skeleton: creating the Tauri app, setting up build tooling, configuring CSS frameworks, adding foundational libraries.

## Required Skills

None

## Work Procedure

1. **Read the feature description carefully.** Understand exactly what needs to be scaffolded.

2. **Check existing state.** Read package.json, Cargo.toml, and directory structure to understand what already exists vs. what needs to be created.

3. **Execute setup commands.** Run the scaffolding commands (create-tauri-app, pnpm install, etc.). Verify each command succeeds before proceeding.

4. **Configure files.** Edit vite.config.ts, tsconfig.json, tailwind.config.cjs, components.json, etc. per the spec requirements. Reference the cc-switch project at `/Users/happypeet/Documents/Github/cc-switch/` for exact configuration patterns.

5. **Create foundational files.** Set up directory structure, create placeholder files (index.css with Tailwind directives, main.tsx with providers, etc.).

6. **Verify the build.** Run:
   - `pnpm tauri dev` — verify it starts without errors (kill after confirming)
   - `cd src-tauri && cargo check` — verify Rust compiles
   - `pnpm typecheck` — verify TypeScript compiles

7. **Write baseline tests.** Create at least one trivial test for Rust (cargo test) and one for frontend (vitest) to confirm test infrastructure works.

8. **Commit with a descriptive message.**

## Example Handoff

```json
{
  "salientSummary": "Scaffolded Tauri 2 + React 18 + Vite project with TailwindCSS 3, shadcn/ui (button, card, dialog, badge, progress, switch, input, sonner, scroll-area), i18next (en/zh), and theme provider. Ran `pnpm tauri dev` successfully, `cargo check` passed, `pnpm typecheck` passed.",
  "whatWasImplemented": "Complete project skeleton: Tauri 2.8 with React 18 + Vite 7, TailwindCSS 3 with shadcn/ui components, i18next with en.json and zh.json locale files, ThemeProvider, react-query setup, path aliases, vite dev server on port 1420.",
  "whatWasLeftUndone": "",
  "verification": {
    "commandsRun": [
      { "command": "pnpm tauri dev (started, verified window opens, killed)", "exitCode": 0, "observation": "Tauri window opened with React app, no console errors" },
      { "command": "cd src-tauri && cargo check", "exitCode": 0, "observation": "Compiles with no errors or warnings" },
      { "command": "pnpm typecheck", "exitCode": 0, "observation": "No TypeScript errors" },
      { "command": "cd src-tauri && cargo test", "exitCode": 0, "observation": "1 test passed" },
      { "command": "pnpm test:unit", "exitCode": 0, "observation": "1 test passed" }
    ],
    "interactiveChecks": []
  },
  "tests": {
    "added": [
      { "file": "src-tauri/src/lib.rs", "cases": [{ "name": "test_app_compiles", "verifies": "basic compilation" }] },
      { "file": "src/__tests__/setup.test.ts", "cases": [{ "name": "vitest works", "verifies": "test infrastructure" }] }
    ]
  },
  "discoveredIssues": []
}
```

## When to Return to Orchestrator

- `pnpm create tauri-app` fails or produces unexpected output
- Rust compilation fails due to missing system dependencies
- Cannot install Tauri CLI

---
> Source: [iamzhihuix/droid-account-manage](https://github.com/iamzhihuix/droid-account-manage) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
