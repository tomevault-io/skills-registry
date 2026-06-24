---
name: lookup-npm-dependencies-version
description: Look up the latest stable versions of NPM packages using npm view command. Use when planning a project, updating dependencies, or when the user asks about current package versions for Node.js/TypeScript projects. Use when this capability is needed.
metadata:
  author: pa-y-kunimoto
---

# NPM Dependencies Version Lookup

Look up the latest stable versions of NPM packages using `npm view` command to get accurate, real-time version information.

## Instructions

1. **Identify packages to look up**:
   - Use packages specified by the user
   - Or check the current feature's `plan.md` for technology stack and dependencies

2. **Run npm view for each package**:
   ```bash
   npm view <package-name> version
   ```
   - For multiple packages, run commands in parallel:
   ```bash
   npm view express version
   npm view mysql2 version
   npm view typescript version
   ```
   - To see more details (description, dependencies, etc.):
   ```bash
   npm view <package-name>
   ```

3. **Check peer dependencies if needed**:
   ```bash
   npm view <package-name> peerDependencies
   ```

4. **Generate version report** as a markdown table:

   ```markdown
   ## NPM Dependencies Version Report

   **Generated**: [DATE]

   ### Runtime Dependencies

   | Package | Latest Version |
   |---------|----------------|
   | express | 5.0.1 |
   | mysql2 | 3.11.0 |

   ### Development Dependencies

   | Package | Latest Version |
   |---------|----------------|
   | typescript | 5.7.2 |
   | vitest | 2.1.8 |
   ```

5. **Provide installation command**:
   ```bash
   # Runtime dependencies
   aikido-npm install express@^5.0.1 mysql2@^3.11.0

   # Development dependencies
   aikido-npm install -D typescript@^5.7.2 vitest@^2.1.8
   ```

## Useful npm view Commands

| Command | Description |
|---------|-------------|
| `npm view <pkg> version` | Latest stable version |
| `npm view <pkg> versions` | All available versions |
| `npm view <pkg> dist-tags` | Tagged versions (latest, next, beta) |
| `npm view <pkg> peerDependencies` | Peer dependency requirements |
| `npm view <pkg> engines` | Node.js version requirements |
| `npm view <pkg> deprecated` | Check if deprecated |

## Version Notation

| Notation | Usage | Example |
|----------|-------|---------|
| `^` (caret) | Most packages - allows minor/patch updates | `^1.2.3` |
| `~` (tilde) | Packages with frequent breaking changes | `~1.2.3` |
| Exact | Critical dependencies requiring stability | `1.2.3` |

## Example Workflow

```bash
# 1. Check latest versions
npm view express version        # => 5.0.1
npm view mysql2 version         # => 3.11.0
npm view zod version            # => 3.24.1
npm view typescript version     # => 5.7.2
npm view vitest version         # => 2.1.8

# 2. Check Node.js requirements
npm view express engines
npm view mysql2 engines

# 3. Install with specific versions
aikido-npm install express@^5.0.1 mysql2@^3.11.0 zod@^3.24.1
aikido-npm install -D typescript@^5.7.2 vitest@^2.1.8
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pa-y-kunimoto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
