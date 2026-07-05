---
name: debug-cucumber-html-report
description: Debug or inspect the Cucumber HTML reporter output in the playwright-bdd project. Use when: investigating DOM structure changes in the Cucumber HTML reporter, updating test selectors or POMs after reporter upgrades, verifying what attachments/hooks/steps look like in the generated HTML report. Use when this capability is needed.
metadata:
  author: vitalets
---

# Debug Cucumber HTML Report

## When to Use
- Inspecting the Cucumber HTML report DOM structure after a reporter version upgrade
- Updating test selectors in `check-report`
- Verifying how steps, hooks, attachments, screenshots, traces appear in the report

## Opening the Report

Always use **`playwright-cli`** with the `PLAYWRIGHT_MCP_ALLOW_UNRESTRICTED_FILE_ACCESS` environment variable to open local report files. Do NOT use Playwright MCP (`@playwright/mcp`).

Run `playwright-cli --help` to see the full list of available commands (open, snapshot, eval, click, goto, etc.).

---
> Source: [vitalets/playwright-bdd](https://github.com/vitalets/playwright-bdd) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
