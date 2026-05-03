---
name: therefore-ui-token-auditor
description: >- Use when this capability is needed.
metadata:
  author: mtmitchel
---

# therefore UI token auditor

## Solo-builder stance

Operate for a solo builder, not a multi-team org.
- Prefer the highest-leverage token fixes one maintainer can apply now.
- Avoid heavyweight process, approval chains, owner matrices, or handoff docs unless the user explicitly asks.
- Keep audit output practical for one person to implement directly.

## Mission
Catch and correct styling drift early by enforcing token-only styling and calm, confident UI constraints.

## Minimum questions (ask only if missing)
- Which files/screen/module should be audited (or “audit the PR diff”)?
- Is the target React UI, CSS/Tailwind, or docs/examples?

## Non-negotiables
- No raw hex/rgb/hsl colors in UI styling.
- No hardcoded pixel values for spacing/sizing/typography in UI styling.
- No blur effects (`backdrop-filter`, `backdrop-blur-*`, `filter: blur()`).
- No sub-14px text for functional UI (labels, buttons, inputs, helper text).
- No all-caps styling (`uppercase`, `[text-transform:uppercase]`, or CSS `text-transform: uppercase`). If a label needs emphasis, use weight or size, not case.

## Workflow
1. Scan the relevant files/diff for violations (colors, sizes, text size, blur).
2. For each violation, propose a token-compliant replacement.
3. If no token exists, propose a new token (name + value + intended usage), and explain why existing tokens don’t fit.

## How to scan (preferred)
Run the bundled scanner to get file/line anchors and draft fixes:
- From repo root:
  - `python3 .codex/skills/therefore-ui-token-auditor/scripts/audit_ui_tokens.py --git-diff` (review current diff)
  - `python3 .codex/skills/therefore-ui-token-auditor/scripts/audit_ui_tokens.py components/modules/canvas styles/components` (targeted paths)
- From the skill folder:
  - `python3 scripts/audit_ui_tokens.py --git-diff`

If the scanner output suggests an ambiguous token choice, resolve it by checking:
- `docs/design-system-overview.md` (principles + hard rules)
- `docs/architecture/island-architecture.md` (island layout contract)
- `docs/technical/design-tokens-reference.md` (canonical conventions and token usage)
- `styles/theme/colors.css`, `styles/theme/layout.css`, `styles/theme/typography.css` (ground-truth token definitions)
- `references/cheatsheet.md` (quick patterns for token-based Tailwind utilities)

## Output format (use exactly)

**Summary:**
- <overall drift severity + 1–3 bullets>

**Scope:**
- **Files reviewed:** <paths>
- **UI types:** <React TSX | CSS | Tailwind>

**Checks performed:**
- [ ] Raw colors (hex/rgb/hsl) removed in favor of tokens
- [ ] Tailwind palette colors removed in favor of tokens
- [ ] Hardcoded px values removed in favor of tokens
- [ ] No blur/backdrop-filter usage
- [ ] Island layout contract respected (shell provides outer padding/gaps; modules avoid double-padding)
- [ ] Functional UI text is 14px+ (12px only for passive metadata)

**Findings:**

For each violation:
- **Location:** `path:line` (or nearest snippet)
- **Issue:** <rule violated>
- **Fix:** <exact replacement (token/class/utility)>
- **Notes:** <only if a new token is required or there’s a tradeoff>

**Recommendations:**
- <prioritized follow-ups (optional): consolidate shared utilities, propose missing tokens>

**Verification:**
- <how to confirm drift is resolved (rerun scanner, run UI smoke checks)>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mtmitchel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
