---
name: web-scraping
description: Guidelines for scraping financial data (Selenium/Soup). Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Web Scraping Skill

## Tools

- **Static Content**: Use `aiohttp` + `BeautifulSoup` (Fast, low resource).
- **Dynamic Content**: Use `selenium` (Heavy, browser automator).

## CME FedWatch Strategy

The CME FedWatch tool usually dynamically loads data via JS.

1. Try to find the internal API endpoint by inspecting the Network Tab manually first.
2. If API is hidden/encrypted, use Selenium in **Headless Mode**.
3. **Rate Limiting**: RESPECT the target. Poll no faster than once every 60s unless critical. High frequency polling will get the IP banned.

## Selectors

- Use robust CSS selectors or XPath.
- Expect the DOM to change. Wrap scraping logic in `try/except` blocks and log failures loudly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
