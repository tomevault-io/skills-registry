---
name: actionbook
description: Browser automation via ActionBook action manuals. Use for web interactions, deployment verification, visual testing, and admin UI tasks. 10x faster than raw browser automation with 100x token savings. Use when this capability is needed.
metadata:
  author: toonvos
---

# ActionBook - Pre-Built Browser Workflows

ActionBook provides **action manuals** - pre-built, reusable browser automation workflows that are 10x faster and use 100x fewer tokens than raw Playwright automation.

## Purpose

Use ActionBook when you need to interact with websites in **predictable, repeatable ways**:

- ✅ Deployment verification (is the app live after deploy?)
- ✅ Visual testing and screenshot capture
- ✅ Admin UI interactions (Prisma Studio, admin panels)
- ✅ Marketing tasks (analytics setup, social media)
- ✅ Any task with a known, reusable browser workflow

**Key benefit:** Action manuals are pre-tested, optimized workflows that work reliably without trial-and-error debugging.

---

## Available Tools

ActionBook provides 3 MCP tools:

| Tool            | Purpose                            | Example                                |
| --------------- | ---------------------------------- | -------------------------------------- |
| `searchActions` | Find action manuals by keyword     | Search for "login", "checkout", "form" |
| `getActionById` | Get specific action manual details | Retrieve manual by ID                  |
| `list_sources`  | List available action sources      | See all available manuals              |

**CRITICAL:** You MUST use `ToolSearch("actionbook")` BEFORE calling these tools to load them.

---

## Quick Start

```typescript
// 1. Load ActionBook tools (REQUIRED first step)
ToolSearch("actionbook");

// 2. Search for relevant action manual
searchActions({ query: "deployment verification" });

// 3. Get specific manual details
getActionById({ id: "verify-app-live" });

// 4. Execute the action manual
// (Follow the manual's instructions)
```

---

## Decision Tree: When to Use ActionBook

### Use ActionBook when:

- ✅ Task matches a common web interaction pattern
- ✅ You need reliable, repeatable automation
- ✅ You want to minimize token usage (100x savings)
- ✅ Pre-built workflow exists for your use case

### Use Playwright (E2E) when:

- ⚠️ You need custom test scenarios
- ⚠️ Testing project-specific user flows
- ⚠️ Writing automated test suites
- ⚠️ No action manual exists for your use case

### Use Claude-in-Chrome when:

- ⚠️ Interactive exploration needed
- ⚠️ One-off tasks requiring human-like browsing
- ⚠️ Complex, adaptive workflows
- ⚠️ Real-time debugging and inspection

**Priority:** ActionBook (if manual exists) > Playwright (custom E2E) > Claude-in-Chrome (interactive)

---

## Project-Specific Use Cases

### 1. Deployment Verification

After deploying to Accept or Production:

```typescript
// Search for deployment verification manual
searchActions({ query: "deployment check health" });

// Execute manual to verify:
// - App is accessible
// - Login works
// - Critical pages load
// - No console errors
```

**Value:** Automated post-deploy smoke test without writing custom E2E tests.

---

### 2. Visual Testing

Capture screenshots for design comparison:

```typescript
// Search for screenshot manual
searchActions({ query: "screenshot responsive" });

// Execute manual to capture:
// - Desktop (1920x1080)
// - Tablet (768x1024)
// - Mobile (375x667)
```

**Value:** Consistent screenshot capture across breakpoints for design reviews.

---

### 3. Admin UI Tasks

Interact with admin panels:

```typescript
// Search for admin interaction manual
searchActions({ query: "admin panel crud" });

// Execute manual for:
// - Prisma Studio data inspection
// - Admin panel configuration
// - User management tasks
```

**Value:** Automate repetitive admin tasks without writing custom scripts.

---

### 4. Marketing and Analytics

Setup tracking and marketing tools:

```typescript
// Search for analytics setup manual
searchActions({ query: "google analytics setup" });

// Execute manual for:
// - Analytics code verification
// - Event tracking validation
// - Social media integration checks
```

**Value:** Verify marketing integrations without manual clicking.

---

## Example Workflow

**Scenario:** Verify landing page after deployment

```typescript
// Step 1: Load ActionBook tools
ToolSearch("actionbook");

// Step 2: Search for relevant manual
const results = searchActions({
  query: "landing page verification screenshot",
});

// Step 3: Review results and select manual
// Results show: "Landing Page Health Check" (id: "lp-health-001")

// Step 4: Get full manual details
const manual = getActionById({ id: "lp-health-001" });

// Step 5: Follow manual instructions
// Manual includes:
// - Navigate to URL
// - Check page load time
// - Verify hero section visible
// - Capture screenshot
// - Check console for errors
// - Verify CTA buttons clickable

// Step 6: Execute and report results
```

**Time saved:** 5-10 minutes vs writing custom Playwright test

**Tokens saved:** ~95% vs raw browser automation

---

## Common Patterns

### Pattern 1: Keyword Search

```typescript
// Generic search
searchActions({ query: "login" });

// Specific search
searchActions({ query: "google oauth login" });

// Multi-keyword
searchActions({ query: "form validation error" });
```

### Pattern 2: Verify Then Screenshot

```typescript
// 1. Find verification manual
searchActions({ query: "app health check" });

// 2. Find screenshot manual
searchActions({ query: "responsive screenshot" });

// 3. Execute in sequence:
//    - Verify app is healthy
//    - Capture screenshots for review
```

### Pattern 3: Before/After Comparison

```typescript
// Before deployment
searchActions({ query: "screenshot capture" });
// Execute manual, save screenshots to /before/

// After deployment
// Execute same manual, save screenshots to /after/

// Compare with visual diff tool
```

---

## Integration with This Project

ActionBook complements our existing browser automation:

| Tool                 | Purpose              | Location            |
| -------------------- | -------------------- | ------------------- |
| **ActionBook**       | Pre-built workflows  | MCP server (global) |
| **Playwright E2E**   | Custom test suites   | `/e2e-tests/`       |
| **Claude-in-Chrome** | Interactive browsing | MCP server (global) |

**Workflow recommendation:**

1. **Check ActionBook first** - Search for existing manual
2. **Custom Playwright if needed** - Write specific E2E test
3. **Claude-in-Chrome for exploration** - Ad-hoc interactive tasks

---

## Common Use Cases Summary

| Task                     | ActionBook Manual          | Alternative               |
| ------------------------ | -------------------------- | ------------------------- |
| Post-deploy verification | "deployment health check"  | Custom Playwright test    |
| Screenshot capture       | "responsive screenshot"    | Manual browser + DevTools |
| Form testing             | "form validation flow"     | Custom Playwright test    |
| Login flows              | "oauth login"              | Custom auth test          |
| Analytics verification   | "analytics tracking check" | Manual verification       |

---

## Troubleshooting

### "Action manual not found"

- Try broader search terms: "login" instead of "oauth google login"
- Use `list_sources` to see all available manuals
- Fall back to Playwright for custom workflows

### "ToolSearch failed"

- Ensure ActionBook MCP server is installed: `npm i -g @actionbookdev/mcp`
- Restart Claude Code after installation
- Verify `.mcp.json` configuration

### "Manual execution failed"

- Check if target URL is accessible
- Verify no auth walls blocking automation
- Try Claude-in-Chrome for interactive debugging

---

## References

- **ActionBook GitHub:** https://github.com/actionbook/actionbook
- **MCP Package:** `@actionbookdev/mcp`
- **Project MCP Config:** `.mcp.json`
- **Related Skills:** `troubleshooting-guide`, `playwright` (via MCP)
- **Project E2E Tests:** `/e2e-tests/CLAUDE.md`

---

## Token Economics

**ActionBook vs Raw Automation:**

| Approach          | Tokens  | Time      | Reliability          |
| ----------------- | ------- | --------- | -------------------- |
| ActionBook manual | ~500    | 1-2 min   | High (pre-tested)    |
| Raw Playwright    | ~50,000 | 10-30 min | Medium (trial-error) |
| Claude-in-Chrome  | ~20,000 | 5-15 min  | Medium (exploratory) |

**Savings:** 100x tokens, 10x faster, higher reliability

**When ActionBook shines:**

- Repetitive tasks (deployment checks, screenshots)
- Standard workflows (login, forms, navigation)
- Pre-defined patterns (analytics setup, admin tasks)

**When to skip ActionBook:**

- No relevant manual exists
- Highly custom project-specific flows
- One-off exploratory tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
