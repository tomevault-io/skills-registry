---
name: feature-inventory
description: How to use, extend, and maintain the auto-generated feature inventory pipeline (FEATURES.json, FEATURES.md, GET /api/features) Use when this capability is needed.
metadata:
  author: qdouble
---

# Feature Inventory

The feature inventory pipeline auto-generates a structured catalog of every capability in the project. It produces two committed artifacts (`FEATURES.json` and `FEATURES.md`) and exposes them via `GET /api/features`.

## When to Load This Skill

- Before adding new IPC channels, HTTP endpoints, renderer panels, skills, workflows, or scripts
- Before modifying extractor logic or the renderer registry
- When debugging why a capability is missing from the inventory
- During REVIEW/AUDIT of changes to `scripts/lib/extractors/`

---

## Commands

```bash
# Generate inventory (writes FEATURES.json + FEATURES.md)
npm run features:generate

# Generate with mobile companion views included
CC_MOBILE_ROOT=../command-center-mobile npm run features:generate

# CI drift check — exits 1 if committed artifacts are stale
npm run features:check

# Query capabilities via HTTP
curl "http://127.0.0.1:3050/api/features" | jq
curl "http://127.0.0.1:3050/api/features?kind=ipc&q=deploy" | jq
curl "http://127.0.0.1:3050/api/features?surface=gui" | jq
```

---

## How Capabilities Are Discovered

| Kind | Parser | Source Files | Key Logic |
|------|--------|-------------|-----------|
| `endpoint` | TypeScript AST | `electron/*.ts` | Matches `*.get/post/put/delete/patch('/path', ...)` calls (receiver-agnostic) |
| `ipc` | TypeScript AST | `electron/*.ts` | Matches `ipcMain.handle('channel', ...)` and `ipcMain.on('channel', ...)` |
| `panel` | Declarative registry | `electron/renderer-registry.ts` | Reads `UI_PANELS` array — no parsing needed |
| `skill` | YAML frontmatter | `.agent/skills/*/SKILL.md`, `.agent/skills/*.md` | Extracts `name` and `description` from frontmatter block |
| `workflow` | YAML frontmatter | `.agent/workflows/*.md` | Extracts `description` from frontmatter block |
| `script` | Header comments | `scripts/**/*.sh`, `scripts/**/*.mjs` | First comment line after shebang becomes the description |
| `view` | Swift regex | `Sources/**/*.swift` (mobile repo) | Matches `struct *View` and `class *ViewModel` declarations |

### Canonical Source Rules

- Only `.ts` files in `electron/` are scanned for IPC/HTTP — generated `.js` mirrors are ignored
- `.test.ts` and `.d.ts` files are excluded
- Mobile views are **opt-in** — only included when `--mobile-root` or `CC_MOBILE_ROOT` is set
- The generator does **not** inventory itself (`.ts` scripts in `scripts/` are not scanned for IPC/HTTP)

---

## Adding a New Capability

### New IPC channel or HTTP endpoint

No action needed — the AST extractors pick these up automatically. Run `npm run features:generate` after committing.

### New renderer panel

Add an entry to the `UI_PANELS` array in `electron/renderer-registry.ts`:

```typescript
{
    name: 'My Panel',
    view: 'composer',
    entryFn: 'renderMyPanel',
    sourcePath: 'electron/renderer-my-panel.ts',
    description: 'What this panel does.'
}
```

### New skill or workflow

Include YAML frontmatter at the top of the `.md` file:

```markdown
---
name: my-skill
description: What this skill teaches agents
---
```

### New script

Add a comment header in the first few lines:

```bash
#!/bin/bash
# What this script does
```

---

## Adding a New Extractor

If you need to inventory a new capability kind (e.g., MCP tools, cron jobs):

1. Create `scripts/lib/extractors/my-extractor.ts`
2. Import shared helpers from `scripts/lib/extractors/shared.ts`:
   - `toPosixPath` — normalize paths to forward slashes
   - `listTypeScriptFiles` — recursive `.ts` file listing (excludes `.test.ts`, `.d.ts`)
   - `readLeadingDescription` — extract JSDoc/comment above an AST node
   - `readStringLiteral` — safely read string literal from AST expression
   - `parseFrontmatter` — parse YAML frontmatter from markdown files
3. Export a function matching the signature: `(rootDir: string) => Capability[]`
4. Add the new `kind` to `CapabilityKind` in `scripts/lib/types.ts`
5. Add the kind label to `KIND_ORDER` and `KIND_LABELS` in `scripts/lib/render-markdown.ts`
6. Wire it into `buildInventory()` in `scripts/generate-features.ts`
7. Add tests in `scripts/generate-features.test.ts`
8. Run `npm run features:generate` and commit the updated artifacts

---

## Schema

`FEATURES.json` follows this structure:

```typescript
interface FeatureInventory {
    schemaVersion: 1;
    generatedAt: string;          // ISO 8601 timestamp
    generatedBy: 'scripts/generate-features.ts';
    commitSha: string;            // git HEAD at generation time
    capabilities: Capability[];   // sorted by id → sourcePath → sourceLine
}

interface Capability {
    id: string;           // e.g. "endpoint:http:GET:/api/health", "ipc:main:send-prompt"
    kind: CapabilityKind; // 'endpoint' | 'ipc' | 'skill' | 'workflow' | 'script' | 'panel' | 'view'
    surface: CapabilitySurface; // 'http' | 'main' | 'agent' | 'cli' | 'gui' | 'mobile-*'
    name: string;
    method?: string;      // HTTP method (endpoints only)
    sourcePath: string;   // relative posix path from project root
    sourceLine?: number;  // 1-indexed line number
    description: string;
}
```

### ID Format

- Endpoints: `endpoint:http:{METHOD}:{path}` (4 segments — prevents collisions when same path has GET + POST)
- All other kinds: `{kind}:{surface}:{name}` (3 segments)

---

## `--check` Mode (CI)

`npm run features:check` regenerates the inventory in memory and compares against committed files. It **never mutates** the working tree. Metadata fields (`generatedAt`, `commitSha`) are read from the committed `FEATURES.json` to prevent false drift from timestamp/SHA changes.

Exit codes:
- `0` — inventory is up to date
- `1` — drift detected, run `npm run features:generate`

---

## API Endpoint

`GET /api/features` serves the committed `FEATURES.json` with optional query filters:

| Param | Effect |
|-------|--------|
| `kind` | Filter by capability kind (e.g., `?kind=ipc`) |
| `surface` | Filter by surface (e.g., `?surface=gui`) |
| `q` | Free-text search across name and description |

Returns `404` if `FEATURES.json` doesn't exist yet.

---

## File Reference

| File | Purpose |
|------|---------|
| `scripts/generate-features.ts` | CLI orchestrator |
| `scripts/lib/types.ts` | `Capability` and `FeatureInventory` interfaces |
| `scripts/lib/extractors/shared.ts` | Shared helpers (5 functions) |
| `scripts/lib/extractors/ipc-extractor.ts` | IPC channel extractor |
| `scripts/lib/extractors/http-extractor.ts` | HTTP endpoint extractor |
| `scripts/lib/extractors/skill-extractor.ts` | Agent skill extractor |
| `scripts/lib/extractors/workflow-extractor.ts` | Agent workflow extractor |
| `scripts/lib/extractors/script-extractor.ts` | Shell/mjs script extractor |
| `scripts/lib/extractors/panel-extractor.ts` | Renderer panel extractor |
| `scripts/lib/extractors/mobile-extractor.ts` | Mobile view/viewmodel extractor |
| `scripts/lib/render-markdown.ts` | JSON → Markdown renderer |
| `electron/renderer-registry.ts` | Declarative UI panel registry |
| `electron/feature-inventory-routes.ts` | `GET /api/features` route |
| `scripts/generate-features.test.ts` | Unit tests (276 lines) |
| `FEATURES.json` | Generated canonical inventory |
| `FEATURES.md` | Generated human-readable inventory |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qdouble) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
