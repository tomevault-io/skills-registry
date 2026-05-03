---
name: frontend-dev
description: Frontend development workflow with visual feedback using screenshots Use when this capability is needed.
metadata:
  author: tristanstreich
---

# Frontend Development Skill

This skill helps you develop and iterate on the React frontend with visual feedback.

## When to Use This Skill

Use this skill when:
- Making CSS changes to the UI
- Adding or modifying React components
- Testing visual changes to the frontend
- Debugging layout issues
- Any frontend work that requires visual verification

## What This Skill Does

1. Starts the development server (`npm run dev`)
2. Sets up the visual feedback loop using Puppeteer screenshots
3. Provides helpers for capturing screenshots with any webpage interactions
4. Guides you through an iterative development process

## Instructions for Claude

When this skill is invoked, follow these steps:

### Step 1: Start Development Server

```bash
npm run dev
```

Wait 5 seconds and verify the server started successfully:
- Backend should show "Server is running on port 2424"
- Frontend watcher should show "Watching frontend/src/*/*"

### Step 2: Establish Screenshot Capability

Use the puppeteer agent to capture screenshots for you (see `.claude/agents/puppeteer.md`)

### Step 3: Visual Feedback Loop

For each change:

1. **Before changing code:** Capture "before" screenshot
2. **Make the change:** Edit CSS/component files
3. **Wait for rebuild:** ~8 seconds for frontend changes
4. **Capture "after" screenshot:** Verify the change worked
5. **Compare:** Show user both screenshots or describe the difference

### Step 4: Verify Changes Work

Always verify at multiple scroll positions if the change affects layout:
- Top of page
- Middle (if applicable)
- Bottom of page

**Required:** Test at multiple viewport sizes for every visual change:
- Desktop: 1280x800 or 1920x1080
- Mobile: 375x667 (iPhone SE/standard)

Mobile verification is mandatory because:
- Text wrapping behavior differs significantly
- Fixed heights can cause overlapping content on mobile
- Touch targets need adequate spacing

Use the advanced screenshot tool for quick mobile verification:
```bash
node scripts/screenshot/screenshot-advanced.js --width=375 --height=667 --output=/tmp/mobile.png
```

## Tips

- **Hot reload timing:** Frontend rebuilds take ~8 seconds, backend ~2-3 seconds
- **Screenshot timing:** Always wait at least 2 seconds after navigation for React to render
- **Failed database connections:** If backend crashes due to DB, restart server after user fixes connection
- **Multiple changes:** Batch related changes together, then verify with screenshots
- **CSS specificity:** Check DevTools if styles aren't applying as expected

## Example Session

```
User: Fix the spacing on the artist list

Claude: I'll start the dev server and establish the visual feedback loop.

[Starts server]
[Captures current state screenshot]
[Identifies spacing issue in CSS]
[Makes CSS change]
[Waits for rebuild]
[Captures new screenshot]

The spacing is now fixed! Here's the before/after comparison.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tristanstreich) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
