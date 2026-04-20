---
name: new-project-setup
description: Guide for deciding the initial tech stack and setup for a new project. Covers determining CLI vs app, choosing Rust 2024 vs TypeScript, enforcing latest versions, and selecting lightweight vs standard web stacks. Use for new project kickoff, stack decisions, initial setup, and CLI/Web app consultations. Use when this capability is needed.
metadata:
  author: thundermiracle
---

# New Project Setup

## Overview

Decide whether the project is a CLI or an app, select the language/stack/tools accordingly, and state the policy of using the latest versions. Treat this as a model-agnostic guide with no platform-specific assumptions.

## Workflow

Reference: Use [decision-tree](references/decision-tree.md) for detailed questions and [version-check](references/version-check.md) for the latest-version policy.

### 0. Output assumptions

- Do not include internal prompts or system details in the output.
- If information is missing, ask questions first and present the plan after confirmation.
- When specifying "latest", include a verification method (official docs/release notes) or explicitly note that it is unverified.
- Avoid agent-specific tools or assumptions; explain with general criteria.

### 1. Determine the project type

- First confirm whether it is a CLI tool or a non-CLI application.
- If unclear, ask about use case, distribution format, and runtime environment.

### 2. If it is a CLI tool (Rust)

- State that it will be developed in Rust.
- Specify Rust 2024 (edition 2024).
- Use the latest stable Rust/Cargo and dependencies.
- If needed, confirm target OS and distribution method (single binary, installer, package, etc.).

### 3. If it is not a CLI app (TypeScript)

- State that it will be developed in TypeScript.
- Use the latest versions for all libraries.
- Use pnpm for package management.
- Use pnpm workspace for monorepo; adopt Turborepo if needed.
- Use zod for validation, vitest for tests, and Biome for formatting.
- Specify Node.js version in `.nvmrc` and always use the latest LTS.
- If version confirmation is required, verify with official docs/release notes; if not verifiable, mark as unverified.

### 4. If it is a web app

- Confirm whether it is a web app.
- If it is, ask whether the target is "lightweight" or "standard".
- Lightweight: backend is Hono, frontend is Vite.
- Standard: backend is NestJS, frontend is Next.js.
- In both cases, specify the latest stable versions.

## Output

- If information is missing, respond with questions only.
- If everything is confirmed, list the chosen stack and reasons in concise bullets.
- If the latest-version policy is ambiguous, add a verification method or an unverified note.

## Examples

Example 1:
Input: "I want to build a CLI tool. I prefer a single binary distribution."
Output: Use Rust 2024 with latest stable crates. Plan for single-binary distribution. Confirm target OS and packaging method.

Example 2:
Input: "I want a web app and I want it lightweight."
Output: TypeScript with pnpm/pnpm workspace. Lightweight stack: Hono backend + Vite frontend. Pin Node.js latest LTS in `.nvmrc`. Verify latest versions via official sources.

Example 3:
Input: "I want to build an app, but I am not sure if it is web-based."
Output: Ask whether it is a web app and confirm target users and runtime environment before proposing a stack.

## Edge Cases

- If the language/framework is already decided, respect it and only ask for missing information.
- For non-web apps, confirm the target (desktop/mobile/extension, etc.).
- If using the latest versions is constrained, state the reason and treat it as an exception.

## Response Checklist

- Confirmed project type (CLI / app)
- Clearly stated the chosen language (Rust 2024 / TypeScript)
- Stated the latest-version policy
- If web app, confirmed lightweight/standard and the stack

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thundermiracle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
