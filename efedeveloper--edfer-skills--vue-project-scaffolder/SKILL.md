---
name: vue-project-scaffolder
description: Scaffold a production-ready Vue 3 + Vite + Tailwind v4 project with full automation and validation. Creates properly configured projects with all dependencies validated and ready to run. Use when this capability is needed.
metadata:
  author: EfeDeveloper
---

# Vue Project Scaffolder

Guide the user through setting up a production-ready Vue 3 project with Vite, Tailwind CSS v4, and shadcn/vue. Validate configurations and dependencies as they progress.

## When to Use This

When user says things like:
- "vue project"
- "create vue project"
- "new vue project"
- "scaffold vue"
- "setup vue"
- "vue project my-app"

## How It Works

This skill **automatically creates and fully configures** a production-ready Vue 3 project in 4 steps:

1. **Creates Vite project** with TypeScript template
2. **Installs dependencies** — Tailwind v4, utilities, and all required packages
3. **Runs setup script** — `node scripts/setup.js` handles file copying, validation, and auto-upgrades
4. **Reports success** — Project is ready for development

**Result**: A fully configured, ready-to-run Vue 3 + Tailwind v4 project. No manual configuration needed.

## Critical Patterns

### MUST DO (Non-negotiable)
- **ASK user for project name** — Required for project creation
- **ASK user for package manager** — Never assume or default without asking
- **EXECUTE all commands automatically** — Don't just guide, actually run them
- **Verify package manager exists** on user's system before proceeding
- **Validate dependencies BEFORE saying "done"** — Check package.json against minimum requirements
- **DO NOT run `npm run dev`** — User runs the dev server themselves after validation

### Minimum Dependencies (Must Validate)
**dependencies:**
- vue >= 3.5.30
- tailwindcss >= 4.2.2
- @tailwindcss/vite >= 4.2.2
- clsx >= 2.1.1
- class-variance-authority >= 0.7.1
- tailwind-merge >= 3.5.0
- lucide-vue-next >= 1.0.0

**devDependencies:**
- typescript >= 5.9.3
- vite >= 8.0.1
- @vitejs/plugin-vue >= 6.0.5
- @vue/tsconfig >= 0.9.0
- @types/node >= 24.12.0
- tw-animate-css >= 1.4.0
- vite-plugin-vue-devtools >= 8.0.7
- vue-tsc >= 3.2.5

### Technical
- **Tailwind v4** uses `@tailwindcss/vite` plugin (NOT v3 PostCSS config)
- **Zero manual configuration**: All files pre-configured, ready to edit
- **NO `tailwind.config.js`** — v4 is plugin-based only
- **NO `postcss.config.js`** — Vite plugin handles everything

## Code Examples

**Vite config with Tailwind v4 (most critical):**
```typescript
import tailwindcss from '@tailwindcss/vite'
import vue from '@vitejs/plugin-vue'
import path from 'node:path'

export default defineConfig({
  plugins: [vue(), tailwindcss()],
  resolve: {
    alias: { '@': path.resolve(__dirname, './src') },
  },
})
```

**Style imports (only this, no PostCSS):**
```css
@import "tailwindcss";
```

## Information to Gather

**ALWAYS ask the user these questions before starting:**

1. **Project name** (required)
   - "What should the project folder be called?"
   - Example: "my-app", "portfolio", "dashboard"

2. **Package manager** (MUST ASK)
   - "Which package manager do you want to use: **pnpm**, **npm**, **yarn**, or **bun**?"
   - This is CRITICAL — all subsequent commands must be adapted to the chosen manager
   - If user doesn't specify: Check what's available on their system, suggest pnpm as default
   - DO NOT skip this question

3. **shadcn** (optional, for later)
   - Ask: "Do you want to add shadcn/vue components? (Optional, can add later)"
   - If yes: Document the init command for them to run after project setup
   - If no: Skip — user can add components anytime with `{pm} dlx shadcn-vue@latest init`

## Implementation Summary

**YOU (the agent) EXECUTE these steps. User does NOT run commands manually.**

Adapt ALL commands to the chosen package manager. Use the **Package Manager Equivalents** table below.

### Execution Steps (Agent Runs These)

1. Verify prerequisites (Node.js >= 20.19.0, package manager available)
2. **EXECUTE:** Create Vite project: `{pm} create vite@latest {project-name} --template vue-ts`
3. **EXECUTE:** Navigate: `cd {project-name}`
4. **EXECUTE:** Install dependencies: `{pm} add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next`
5. **EXECUTE:** Install dev dependencies: `{pm} add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vue-tsc`
6. **EXECUTE:** Run the automated setup script:
   ```bash
   node scripts/setup.js {project-dir} {package-manager}
   ```
   
   This script automatically:
   - ✅ Copies config files from `assets/`
   - ✅ Removes incompatible files (Tailwind v4)
   - ✅ Validates all 15 dependencies
   - ✅ Auto-upgrades outdated packages
   - ✅ Verifies final configuration

7. **VERIFY:** Confirm setup completed successfully by checking:
   - Script output shows all ✅ checkmarks
   - No ❌ errors in validation
   - All 6+ files copied successfully

8. **DO NOT EXECUTE:** 
   - Do NOT run `{pm} run dev` — user will do this themselves
   - Do NOT run `shadcn-vue init` — if user wants it, they'll add components later
   - Do NOT make manual edits to config files — the script handles everything

9. **Report:** Show user the success summary and next steps

**For detailed explanations**, see [`references/implementation-guide.md`](references/implementation-guide.md)

## Success Criteria

**BEFORE saying "done", validate EVERY item below:**

### File Structure
- ✅ `vite.config.ts` exists with `@tailwindcss/vite` plugin and `@/` alias
- ✅ `tsconfig.json`, `tsconfig.app.json`, and `tsconfig.node.json` all exist with correct config
- ✅ `tsconfig.node.json` has `moduleResolution: "bundler"` (fixes vite.config.ts type errors)
- ✅ `src/style.css` exists with `@import "tailwindcss"` and `@import "tw-animate-css"`
- ✅ `src/App.vue` exists with gradient content
- ✅ `README.md` exists in project root with setup and next-steps documentation

### Dependencies in package.json
**Check these MUST be in dependencies:**
- ✅ vue >= 3.5.30
- ✅ tailwindcss >= 4.2.2
- ✅ @tailwindcss/vite >= 4.2.2
- ✅ clsx >= 2.1.1
- ✅ class-variance-authority >= 0.7.1
- ✅ tailwind-merge >= 3.5.0
- ✅ lucide-vue-next >= 1.0.0

**Check these MUST be in devDependencies:**
- ✅ typescript >= 5.9.3
- ✅ vite >= 8.0.1
- ✅ @vitejs/plugin-vue >= 6.0.5
- ✅ @vue/tsconfig >= 0.9.0
- ✅ @types/node >= 24.12.0
- ✅ tw-animate-css >= 1.4.0
- ✅ vite-plugin-vue-devtools >= 8.0.7
- ✅ vue-tsc >= 3.2.5

### Critical (Should NOT exist)
- ✅ **NO** `tailwind.config.js` (v4 is plugin-based)
- ✅ **NO** `postcss.config.js` (Vite plugin handles it)

### Project Ready
- ✅ User can navigate to project folder
- ✅ User can run `{pm} run dev` to start development
- ✅ All files copied correctly from assets
- ✅ `node_modules/` has all packages installed

## Error Handling

**CRITICAL: Do NOT say "done" if any of these fail. Fix the issue first.**

### Step Failures
If any step fails:
1. Explain what went wrong clearly
2. Show the exact command that failed
3. Suggest fix or troubleshooting steps
4. Retry or ask user if they want to continue

### Dependency Validation Failure
**If `package.json` is missing ANY minimum dependency:**
1. ❌ DO NOT say "project is ready"
2. Check what's missing: `{pm} list`
3. Install missing packages: `{pm} add {missing-package}`
4. Re-validate package.json
5. Only say "done" when ALL dependencies are present

### Common Issues

| Problem | Solution |
|---------|----------|
| Package manager not found | Check if `which pnpm`, `which npm`, etc. work. Ask user to install. |
| Permission denied during creation | Try `sudo` or check folder permissions. |
| Vite creation hangs | Ctrl+C and retry with `--force` flag if needed. |
| Dependencies installation fails | Check internet connection, retry with `--legacy-peer-deps` if npm. |
| Missing minimum dependencies | Install manually: `{pm} add {package}@{version}` |
| `tailwind.config.js` accidentally created | Delete it. v4 uses plugin-based config. |
| Configuration file won't copy | Check file permissions and disk space. |

## Package Manager Equivalents

**NEVER hardcode commands!** Always check which PM the user chose and adapt accordingly.

| Action | pnpm | npm | yarn | bun |
|--------|------|-----|------|-----|
| Create project | `pnpm create vite@latest {name} --template vue-ts` | `npm create vite@latest {name} -- --template vue-ts` | `yarn create vite {name} --template vue-ts` | `bun create vite {name} --template vue-ts` |
| Add dependency | `pnpm add {pkg}` | `npm install {pkg}` | `yarn add {pkg}` | `bun add {pkg}` |
| Add dev dependency | `pnpm add -D {pkg}` | `npm install --save-dev {pkg}` | `yarn add -D {pkg}` | `bun add -D {pkg}` |
| Install Tailwind v4 | `pnpm add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next` | `npm install tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next` | `yarn add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next` | `bun add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next` |
| Install dev tools | `pnpm add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc` | `npm install -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc` | `yarn add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc` | `bun add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc` |
| Init shadcn | `pnpm dlx shadcn-vue@latest init` | `npx shadcn-vue@latest init` | `yarn dlx shadcn-vue@latest init` | `bun x shadcn-vue@latest init` |
| Add shadcn component | `pnpm dlx shadcn-vue@latest add button` | `npx shadcn-vue@latest add button` | `yarn dlx shadcn-vue@latest add button` | `bun x shadcn-vue@latest add button` |
| Run dev server | `pnpm run dev` | `npm run dev` | `yarn dev` | `bun run dev` |

## Commands (Agent Executes These)

**AGENT: Replace `{pm}` with the user's chosen package manager and EXECUTE each command**

### 1. Create Project
```bash
{pm} create vite@latest {project-name} --template vue-ts
cd {project-name}
```

### 2. Install Dependencies
```bash
{pm} add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next
{pm} add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc
```

### 3. Automated Setup
Run the setup script (from skill root directory):
```bash
node scripts/setup.js {full-path-to-project} {package-manager}
```

For detailed script documentation, see [references/SCRIPTS.md](references/SCRIPTS.md).

### 4. STOP HERE - Do NOT Run Dev Server
**DO NOT execute `{pm} run dev` or equivalent**

The user will run it themselves after setup completes:
```bash
cd {project-name}
{pm} run dev
```

### Complete Workflow by Package Manager

**Example with bun:**
```bash
bun create vite my-app --template vue-ts && cd my-app
bun add tailwindcss @tailwindcss/vite clsx class-variance-authority tailwind-merge lucide-vue-next
bun add -D @types/node tw-animate-css typescript vite @vitejs/plugin-vue @vue/tsconfig vite-plugin-vue-devtools vue-tsc
node /path/to/skill/scripts/setup.js $(pwd) bun
bun run dev
```

---

## Final Report (What to Tell User)

After completing all steps and validating dependencies, provide this summary:

```
✅ PROJECT CREATED AND CONFIGURED

Project Name: {project-name}
Location: {project-path}
Package Manager: {pm}

📦 Dependencies Installed:
- Vue 3 (3.5.30+)
- Vite (8.0.1+)
- Tailwind CSS v4 (4.2.2+) with @tailwindcss/vite plugin
- TypeScript (5.9.3+)
- All utilities: clsx, tailwind-merge, lucide-vue-next, tw-animate-css

🎨 Configuration:
- vite.config.ts ✅
- tsconfig.json with @ alias ✅
- tsconfig.app.json with strict rules ✅
- src/style.css with Tailwind imports ✅
- src/App.vue with demo component ✅

⚡ Next Steps:
1. Open your terminal
2. Navigate to the project: cd {project-name}
3. Start development server: {pm} run dev
4. Open http://localhost:5173 in your browser

📚 Optional Next Steps:
- **Add shadcn components:** `{pm} dlx shadcn-vue@latest init` (then add individual components)
- **Add state management:** `{pm} add pinia`
- **Add routing:** `{pm} add vue-router`

**IMPORTANT:**
- ✅ All minimum dependencies are validated and installed
- ✅ Project is ready to develop immediately
- ✅ Tailwind v4 is already configured (NO extra config needed)
- ❌ Do NOT run `{pm} run dev` — user will do this themselves
- ℹ️ Point user to the README.md in the project for detailed next steps

---

## Resources

### Templates (Copy & Use)
- **[assets/vite.config.ts](assets/vite.config.ts)** — Vite configuration with Tailwind v4 and Vue plugins
- **[assets/tsconfig.json](assets/tsconfig.json)** — Root TypeScript config with path aliases
- **[assets/tsconfig.app.json](assets/tsconfig.app.json)** — App TypeScript config with path aliases
- **[assets/tsconfig.node.json](assets/tsconfig.node.json)** — Node TypeScript config for vite.config.ts (CRITICAL - fixes type resolution)
- **[assets/style.css](assets/style.css)** — Minimal style.css with Tailwind import
- **[assets/App.vue](assets/App.vue)** — Demo App.vue with working shadcn Button
- **[assets/components.json](assets/components.json)** — shadcn configuration file
- **[assets/README.md](assets/README.md)** — Project README template

### Scripts Documentation
- **[references/SCRIPTS.md](references/SCRIPTS.md)** — Complete automation scripts guide, troubleshooting, and technical details

### Official Documentation
- [shadcn/vue Installation](https://www.shadcn-vue.com/docs/installation/vite)
- [Tailwind CSS v4 with Vite](https://tailwindcss.com/docs/installation/using-vite)
- [Vite Documentation](https://vite.dev)
- [Vue 3 Guide](https://vuejs.org)

---
> Source: [EfeDeveloper/edfer-skills](https://github.com/EfeDeveloper/edfer-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
