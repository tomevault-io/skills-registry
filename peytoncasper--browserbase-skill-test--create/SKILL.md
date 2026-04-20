---
name: create
description: Create new browser automation scripts step-by-step. Use when starting a new automation from scratch or when the user wants to automate a website task. Use when this capability is needed.
metadata:
  author: peytoncasper
---

# Create Automation Skill

Guide Claude through creating new browser automation scripts using the `browse` CLI.

## When to Use

Use this skill when:
- User wants to automate a website task
- User needs to scrape data from a site
- User wants to create a Browserbase Function
- Starting from scratch on a new automation

## Prerequisites

Requires the `browse` CLI and Chrome/Chromium:
```bash
browse --version  # Check if installed
bash scripts/setup-browse.sh  # Install if needed
```

Install manually: `pnpm add -g @browserbasehq/browse-cli`

## Workflow

### 1. Understand the Goal

Ask clarifying questions:
- What website/URL are you automating?
- What's the end goal (extract data, submit forms, monitor changes)?
- Does it require authentication?
- Should this run on a schedule or on-demand?

### 2. Explore the Site Interactively

The browse CLI auto-starts a Chrome daemon on first command. State persists between commands.

```bash
browse goto https://example.com
browse snapshot
```

Take screenshots to see the visual layout:
```bash
browse screenshot -o exploration.png
```

### 3. Identify Key Elements

For each step of the automation, identify:
- Selectors for interactive elements
- Wait conditions needed
- Data to extract

Use the accessibility tree refs to understand element relationships:
```
[@0-5] button: "Submit"
[@0-6] textbox: "Email"
[@0-7] textbox: "Password"
```

### 4. Test Interactions Manually

Before writing code, verify each step works:

```bash
browse fill @0-6 "test@example.com"
browse fill @0-7 "password123"
browse click @0-5
browse wait networkidle
browse snapshot
```

### 5. Enable Network Capture (if needed)

For API-based automations or debugging:
```bash
browse network on
# perform actions
browse network list
browse network show 0
```

### 6. Create the Function

Once you understand the flow, create a full function project.

For serverless deployment, see the `/functions` skill which covers the `bb` CLI.

For a quick script, write the automation logic:

```typescript
import { chromium } from "playwright-core";

async function automate() {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();

  // Your automation steps here
  await page.goto("https://example.com");
  await page.fill('input[name="email"]', "user@example.com");
  await page.click('button[type="submit"]');
  
  // Extract and return data
  const result = await page.textContent('.result');
  console.log(result);

  await browser.close();
}

automate();
```

### 7. End the Session

When done exploring:
```bash
browse stop
```

## Best Practices

### Selectors
- Prefer data attributes (`data-testid`) over CSS classes
- Use text content as fallback (`text=Submit`)
- Avoid fragile selectors like nth-child

### Waiting
- Always wait for navigation/network after clicks
- Use `waitForSelector` for dynamic content
- Set reasonable timeouts

### Error Handling
- Wrap risky operations in try/catch
- Return structured error information
- Log intermediate steps for debugging

### Data Extraction
- Use `page.evaluate()` for complex extraction
- Validate extracted data before returning
- Handle missing elements gracefully

## Example: E-commerce Price Monitor

```typescript
import { chromium } from "playwright-core";

async function monitorPrice(productUrl: string) {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();

  await page.goto(productUrl);
  await page.waitForSelector('.price');

  const price = await page.evaluate(() => {
    const el = document.querySelector('.price');
    return el?.textContent?.replace(/[^0-9.]/g, '');
  });

  await browser.close();

  return {
    url: productUrl,
    price: parseFloat(price || '0'),
    timestamp: new Date().toISOString(),
  };
}
```

## Next Steps

Once you've tested your automation interactively:

- **For one-time scripts**: Run locally with Node.js
- **For scheduled/webhook automation**: Use `/functions` skill to deploy to Browserbase
- **For authenticated workflows**: See `/auth` skill for handling logins

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peytoncasper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
