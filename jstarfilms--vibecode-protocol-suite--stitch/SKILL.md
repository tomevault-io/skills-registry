---
name: stitch
description: | Use when this capability is needed.
metadata:
  author: jstarfilms
---

# Google Stitch — Skill Bundle

This folder contains 6 sub-skills for working with [Google Stitch](https://stitch.withgoogle.com/), a design-to-code platform with an MCP server. Each sub-skill has its own `SKILL.md` with full instructions.

## Prerequisites

Most skills require the **Stitch MCP Server** to be connected. The `shadcn-ui` skill works independently with the shadcn MCP server or CLI.

---

## 🔀 Pipeline Orchestration Protocol (READ THIS FIRST)

> **YOU MUST follow this protocol.** Do not just load one sub-skill and stop. The Stitch bundle is a **pipeline** — each skill feeds into the next. Your job is to **guide the user through the full journey**, suggesting the next step after each skill completes.

### The Full Pipeline (Design → Code → Video)

```
┌─────────────┐     ┌─────────────────┐     ┌──────────────┐     ┌───────────────┐     ┌──────────┐
│  design-md  │ ──→ │ enhance-prompt  │ ──→ │ stitch-loop  │ ──→ │   react-      │ ──→ │ remotion │
│ STEP 1      │     │ STEP 2          │     │ STEP 3       │     │ components    │     │ STEP 5   │
│             │     │                 │     │              │     │ STEP 4        │     │          │
│ Extract the │     │ Polish prompts  │     │ Build pages  │     │ Convert HTML  │     │ Create a │
│ design      │     │ with design     │     │ iteratively  │     │ to production │     │ video    │
│ system      │     │ tokens from     │     │ using the    │     │ React/TS      │     │ showcase │
│ → DESIGN.md │     │ DESIGN.md       │     │ baton system │     │ components    │     │ of your  │
│             │     │ → next-prompt   │     │ → full site  │     │ → src/        │     │ screens  │
└─────────────┘     └─────────────────┘     └──────────────┘     └───────────────┘     └──────────┘
                                                                        │
                                                                  ┌─────┴──────┐
                                                                  ▼            │
                                                        ┌──────────────┐       │
                                                        │  shadcn-ui   │       │
                                                        │ COMPANION    │       │
                                                        │              │       │
                                                        │ Add polished │       │
                                                        │ UI component │       │
                                                        │ library      │       │
                                                        └──────────────┘       │
```

### How to Orchestrate

**After completing ANY sub-skill, you MUST:**
1. Tell the user what was accomplished
2. Explain what the **next logical step** in the pipeline is
3. Ask if they want to continue to the next step
4. If yes, load that sub-skill's `SKILL.md` and proceed

**Example flow:**
```
✅ design-md complete → "Your DESIGN.md is ready. Next step: I can polish 
   your first page prompt using enhance-prompt so Stitch generates a 
   higher-quality result. Want to continue?"

✅ enhance-prompt complete → "Your prompt is optimized. Next step: I can 
   send this to Stitch and start building pages with stitch-loop. Ready?"

✅ stitch-loop complete → "Your site pages are built. Two options now:
   1. react-components — Convert these HTML pages into production React/TS
   2. remotion — Create a walkthrough video showcasing the screens
   Which would you like?"

✅ react-components complete → "Your React components are ready. You might 
   also want to:
   • Add shadcn/ui components for polished form inputs, buttons, dialogs
   • Create a remotion walkthrough video of the original designs
   Want either of those?"
```

### Decision Tree: Where to Start

Not every user starts at Step 1. Use this decision tree:

| User says... | Start at | Then suggest... |
|---|---|---|
| "Build me a site with Stitch" | `design-md` (Step 1) | Full pipeline |
| "I have a Stitch project, make it consistent" | `design-md` (Step 1) | `enhance-prompt` → `stitch-loop` |
| "Generate a page with Stitch" | `enhance-prompt` (Step 2) | `stitch-loop` |
| "I have Stitch HTML, make it React" | `react-components` (Step 4) | `shadcn-ui` for components |
| "Make a video of my Stitch screens" | `remotion` (Step 5) | Done (standalone) |
| "I need UI components for my React app" | `shadcn-ui` (Companion) | Done (standalone) |
| "Improve my Stitch prompt" | `enhance-prompt` (Step 2) | `stitch-loop` |
| "I already have DESIGN.md, build pages" | `stitch-loop` (Step 3) | `react-components` |

### When to Use Each Skill (Quick Reference)

| Skill | Purpose | Input | Output | Requires |
|---|---|---|---|---|
| `design-md` | Extract design system from existing screens | Stitch project with screens | `DESIGN.md` | Stitch MCP |
| `enhance-prompt` | Polish vague prompts into Stitch-optimized ones | User idea + optional `DESIGN.md` | Enhanced prompt / `next-prompt.md` | None (Stitch MCP optional) |
| `stitch-loop` | Build multi-page sites iteratively | `DESIGN.md` + `next-prompt.md` | Full site in `site/public/` | Stitch MCP |
| `react-components` | Convert Stitch HTML → production React/TS | Stitch screen HTML | React components in `src/` | Stitch MCP |
| `remotion` | Create walkthrough videos from screenshots | Stitch project screenshots | Remotion project + `.mp4` | Stitch MCP + Remotion |
| `shadcn-ui` | Add polished UI component library | Any React/Next.js project | Components in `components/ui/` | shadcn CLI or MCP |

---

## Sub-Skill Directory

Read the linked `SKILL.md` for any sub-skill before using it.

### 1. `design-md` — Design System Extraction (STEP 1)
[→ Read SKILL.md](design-md/SKILL.md)

**What:** Analyzes Stitch project screens and generates a semantic `DESIGN.md` file documenting the visual design system (colors, typography, depth, layout, component styles).

**When to use:**
- Starting a new multi-page project and need a design system reference
- Onboarding to an existing Stitch project
- Need consistent design language across agent-generated screens

**Outputs:** `DESIGN.md` file

**➡️ Next step:** `enhance-prompt` — Use the `DESIGN.md` to craft polished prompts for page generation.

---

### 2. `enhance-prompt` — Prompt Enhancement (STEP 2)
[→ Read SKILL.md](enhance-prompt/SKILL.md)

**What:** Transforms vague UI ideas into polished, structured prompts optimized for Stitch generation. Adds UI/UX keywords, design system context, and page structure.

**When to use:**
- User gives a vague prompt like "make me a login page"
- Previous Stitch generation produced poor results
- Need to inject `DESIGN.md` tokens into a prompt

**Outputs:** Enhanced prompt text or `next-prompt.md` file

**➡️ Next step:** `stitch-loop` — Feed the enhanced prompt into the iterative build loop to generate the actual page.

---

### 3. `stitch-loop` — Iterative Build Loop (STEP 3)
[→ Read SKILL.md](stitch-loop/SKILL.md)

**What:** Autonomous baton-passing loop for building multi-page websites. Each iteration reads a task (`next-prompt.md`), generates a page, integrates it, updates docs, and writes the next task.

**When to use:**
- Building a complete multi-page site from scratch
- Need autonomous, continuous page generation
- Want a structured sitemap-driven build process

**Depends on:** `design-md` (for `DESIGN.md`), `enhance-prompt` (for baton prompts)
**Outputs:** Full site in `site/public/`, updated `SITE.md`

**➡️ Next step:** Choose one or both:
- `react-components` — Convert the generated HTML pages into production-ready React/TypeScript components.
- `remotion` — Create a walkthrough video showcasing the finished screens.

---

### 4. `react-components` — Design to React (STEP 4)
[→ Read SKILL.md](react-components/SKILL.md)

**What:** Converts Stitch-generated HTML into modular React/TypeScript components with proper props interfaces, mock data layers, and AST-based validation.

**When to use:**
- Converting a Stitch screen into production React code
- Need type-safe, modular component extraction from HTML
- Want automated validation of component architecture

**Outputs:** React components in `src/`, mock data in `src/data/mockData.ts`

**➡️ Next step:** Consider:
- `shadcn-ui` — Replace custom UI primitives (buttons, inputs, dialogs) with shadcn/ui components for a polished, accessible component library.
- `remotion` — Create a walkthrough video if you haven't already.

---

### 5. `remotion` — Walkthrough Videos (STEP 5)
[→ Read SKILL.md](remotion/SKILL.md)

**What:** Generates walkthrough videos from Stitch project screenshots using Remotion, with smooth transitions, zoom effects, and text overlays.

**When to use:**
- Need a video showcase of designed screens
- Want to create a professional app walkthrough
- Building a demo reel from Stitch designs

> **Note:** For general Remotion work (not Stitch-specific), use the main `remotion` skill instead — it has 25+ rule files from Remotion's official repo.

**Outputs:** Remotion project + rendered `.mp4`

**➡️ This is typically the final step in the pipeline.** No automatic next step, but you can suggest sharing/publishing the video.

---

### 6. `shadcn-ui` — Component Library (COMPANION)
[→ Read SKILL.md](shadcn-ui/SKILL.md)

**What:** Expert guidance for integrating shadcn/ui components — discovery, installation, customization, variants, blocks, and accessibility. Works with the shadcn MCP server or CLI.

**When to use:**
- Setting up shadcn/ui in a new project
- Installing or customizing shadcn components
- Building forms, data tables, auth layouts, or dashboards
- Need component variant patterns with `cva`
- After `react-components` — replace custom primitives with shadcn/ui

> **Note:** This skill works independently of Stitch. It's useful for any React/Next.js project. Think of it as a **companion skill** that plugs in alongside the main pipeline, especially after `react-components`.

**Outputs:** Installed components in `components/ui/`, configured `components.json`

---

## Typical Workflows

### Full Pipeline (New Project)
```
1. design-md      → Analyze an existing screen → get DESIGN.md
2. enhance-prompt → Write a prompt for the next page → inject design tokens
3. stitch-loop    → Generate page → integrate → write next baton → repeat
4. react-components → Convert final HTML pages to production React
5. remotion       → Create a walkthrough video of the finished product
```

### Quick Build (One Page)
```
1. enhance-prompt → Polish the user's idea into a great prompt
2. Generate directly with Stitch MCP → get HTML
3. react-components → Convert to React (optional)
```

### Design System Only
```
1. design-md → Document an existing project's visual language
```

### Video Only
```
1. remotion → Create a walkthrough video from existing Stitch screenshots
```

### Component Library Only
```
1. shadcn-ui → Set up and customize shadcn/ui components
```

## Standalone Usage
- **`shadcn-ui`** — Use anytime for React/Next.js UI component work (no Stitch needed)
- **`design-md`** — Use standalone to document any project's design system
- **`enhance-prompt`** — Use standalone to improve any UI generation prompt

## Acknowledgements

- Based on [stitch-skills](https://github.com/google-labs-code/stitch-skills)
- This is not an officially supported Google product. This project is not eligible for the [Google Open Source Software Vulnerability Rewards Program](https://bughunters.google.com/open-source-security).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jstarfilms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
