---
name: screenshot
description: Take a screenshot of a local web page using Playwright for visual debugging. Use when you need to see what a page looks like. Use when this capability is needed.
metadata:
  author: tomashrdlicka
---

# screenshot

Take a screenshot of a local web page using Playwright (Chromium) for visual debugging.

## Usage
```
/screenshot                              # Default: http://localhost:8081, viewport screenshot
/screenshot http://localhost:3000        # Custom URL
/screenshot --selector ".my-component"   # Screenshot a specific element
/screenshot --wait 3000                  # Wait 3s for animations/loading before capture
/screenshot --full                       # Full-page screenshot (not just viewport)
/screenshot http://localhost:8081 --wait 5000 --full
```

## Behavior

1. Parse arguments:
   - First positional arg = URL (default: `http://localhost:8081`)
   - `--selector "css"` = screenshot only that element
   - `--wait N` = wait N milliseconds after load before screenshot (default: 2000)
   - `--full` = full page screenshot instead of viewport only
   - `--width N` = viewport width (default: 1440)
   - `--height N` = viewport height (default: 900)
   - `--dark` = inject `localStorage.setItem('yc-dark-mode', 'true')` before navigating

2. Run a Node.js script using `npx playwright` that:
   - Launches headless Chromium
   - Sets viewport to specified dimensions
   - Navigates to URL
   - Waits for network idle + specified wait time
   - Takes screenshot
   - Saves to `/private/tmp/claude-501/screenshots/screenshot-{timestamp}.png`

3. Use the Read tool to view the screenshot image file and analyze it

## Implementation

Write a temporary Node.js script and run it with `node`. The script uses `playwright` (already installed globally).

```javascript
const { chromium } = require('playwright');

(async () => {
    const browser = await chromium.launch();
    const context = await browser.newContext({
        viewport: { width: WIDTH, height: HEIGHT }
    });
    const page = await context.newPage();

    // Optional: set dark mode in localStorage before navigation
    // if (dark) { await page.addInitScript(() => localStorage.setItem('yc-dark-mode', 'true')); }

    await page.goto(URL, { waitUntil: 'networkidle' });
    await page.waitForTimeout(WAIT);

    // Optional: element screenshot
    // if (selector) { await page.locator(selector).screenshot({ path: OUTPUT }); }
    // else { await page.screenshot({ path: OUTPUT, fullPage: FULL }); }

    await page.screenshot({ path: OUTPUT, fullPage: FULL_PAGE });
    await browser.close();
    console.log(OUTPUT);
})();
```

4. After taking the screenshot, ALWAYS use the Read tool to view the PNG file and describe what you see. This is the whole point - visual debugging.

## Important Notes

- Ensure a dev server is running at the target URL before taking a screenshot
- The default 2000ms wait handles most D3 animations and data loading
- For the YC visualizer, use `--wait 5000` to ensure force simulation + entrance animation complete
- Screenshots are saved as PNG for Read tool compatibility
- Create the screenshots directory if it doesn't exist: `mkdir -p /private/tmp/claude-501/screenshots`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomashrdlicka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
