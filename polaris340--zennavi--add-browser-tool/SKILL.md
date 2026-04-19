---
name: add-browser-tool
description: Add a new browser automation tool using AI SDK tool(), Zod schemas, and Chrome DevTools Protocol. Use when implementing click, scroll, hover, or other browser interaction capabilities. Use when this capability is needed.
metadata:
  author: polaris340
---

# Add Browser Tool

Guide for adding a new browser automation tool to Zennavi.

## Step 1: Create the Tool File

Create a new file in `lib/llm/tools/` with a kebab-case name (e.g., `scroll.ts`, `hover.ts`).

## Step 2: Import Dependencies

```typescript
import { tool } from "ai";
import { browser } from "wxt/browser";
import { z } from "zod";
import { throttle } from "~/lib/utils/throttle";
import { BROWSER_AGENT_CONFIG } from "./config";
```

For CDP interactions, also import from `debugger-helper.ts`:

```typescript
import { sendCommand, clickAt, dispatchMouseEvent } from "./debugger-helper";
```

## Step 3: Define Zod Schema

Every tool **must** include `tabId: z.number()`:

```typescript
inputSchema: z.object({
  tabId: z.number().describe("Tab ID to operate on"),
  // Add tool-specific parameters here
}),
```

## Step 4: Implement Execute Function

Follow this pattern:

```typescript
execute: async ({ tabId, ...params }) => {
  try {
    // 1. Throttle
    await throttle.wait(BROWSER_AGENT_CONFIG.throttleMs, "browser");

    // 2. Perform the browser action
    // ... your implementation

    // 3. Return success
    return { success: true, /* additional data */ };
  } catch (error) {
    return {
      success: false,
      error: `Description: ${error instanceof Error ? error.message : String(error)}`,
    };
  }
},
```

## Step 5: Choose the Right API

| API | When to Use | Import |
|-----|-------------|--------|
| Chrome DevTools Protocol (CDP) | Mouse events, keyboard input, screenshots | `debugger-helper.ts` |
| Scripting API | DOM queries, reading content, element state | `browser.scripting.executeScript` |
| Tabs API | Navigation, tab management | `browser.tabs` |

### CDP Example (mouse/keyboard)

```typescript
import { sendCommand } from "./debugger-helper";
await sendCommand(tabId, "Input.dispatchMouseEvent", {
  type: "mousePressed", x, y, button: "left", clickCount: 1,
});
```

### Scripting API Example (DOM query)

```typescript
const results = await browser.scripting.executeScript({
  target: { tabId },
  func: (selector: string) => {
    const el = document.querySelector(selector);
    return el ? { found: true, text: el.textContent } : { found: false };
  },
  args: [selector],
});
```

## Step 6: Register the Tool

In `lib/llm/tools/index.ts`:

1. Add import: `import { myTool } from "./my-tool";`
2. Add to `browsingTools` object: `myAction: myTool,`
3. Add to named exports: `export { myTool };`

## Reference Files

- `lib/llm/tools/click.ts` — CDP + scripting pattern (find element via scripting, click via CDP)
- `lib/llm/tools/navigate.ts` — tabs API pattern (navigation with load detection)
- `lib/llm/tools/read-page.ts` — scripting-only pattern (accessibility tree)
- `lib/llm/tools/debugger-helper.ts` — CDP session management and input helpers
- `lib/llm/tools/config.ts` — shared config (`BROWSER_AGENT_CONFIG`)
- `lib/llm/tools/index.ts` — tool registry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/polaris340) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
