---
name: web-scraping
description: Extracts structured data from websites. Activates when user asks to scrape, crawl, extract data from web pages, or get information from URLs.
version: 1.0.0
triggers:
  - scrape
  - crawl
  - extract from website
  - get data from url
  - parse webpage
  - web scraping
  - pull from site
domain: web
complexity: moderate
dependencies: []
browser: true
author: auto-generated
created: 2025-01-24
---

# Web Scraping

## Overview
Extracts structured data from any website using browser automation. Handles dynamic content, pagination, and common anti-bot measures.

## Auto-Activation Conditions
This skill activates when:
- ✅ User asks to "scrape" or "extract" data from a website
- ✅ Request mentions getting information from URLs
- ✅ Task involves parsing HTML content
- ✅ User wants to collect data from multiple pages

Does NOT activate when:
- ❌ Data is available via public API (use API instead)
- ❌ Simple HTTP request would work (no JS rendering needed)
- ❌ User has the data locally already

## Browser Integration
**Requires**: `@../web-tools/CLAUDE.md`

## Instructions

### Phase 1: Analyze Target
1. Identify the target URL(s)
2. Determine what data needs extraction
3. Plan selector strategy

```python
target = {
    "url": "<user-provided-url>",
    "data_points": ["<field1>", "<field2>"],
    "pagination": True/False,
    "requires_auth": True/False
}
```

### Phase 2: Setup Browser
```python
from browser_use import Agent, Browser, ChatBrowserUse
import asyncio

browser = Browser(
    headless=True,  # Set False for debugging
    timeout=30000,
)
```

### Phase 3: Navigate and Extract
```python
async def scrape(url, selectors):
    await browser.goto(url)
    await browser.wait_for_load()
    
    data = {}
    for name, selector in selectors.items():
        try:
            elements = await browser.get_all(selector)
            data[name] = [e.text for e in elements]
        except:
            data[name] = []
    
    return data
```

### Phase 4: Handle Pagination (if needed)
```python
async def scrape_all_pages(start_url, selectors, next_selector, max_pages=10):
    all_data = []
    await browser.goto(start_url)
    
    for page in range(max_pages):
        # Extract current page
        page_data = await extract_page(selectors)
        all_data.extend(page_data)
        
        # Try next page
        next_btn = await browser.query(next_selector)
        if not next_btn:
            break
        
        await browser.click(next_selector)
        await browser.wait_for_load()
        await asyncio.sleep(1)  # Polite delay
    
    return all_data
```

### Phase 5: Return Structured Data
```python
return {
    "success": True,
    "source": url,
    "timestamp": datetime.now().isoformat(),
    "total_items": len(data),
    "data": data
}
```

## Selector Strategy

### Priority Order
1. `[data-testid="x"]` - Most stable
2. `#id` - Unique identifiers
3. `[name="x"]` - Form fields
4. `[aria-label="x"]` - Accessibility
5. `.class` - CSS classes (less stable)

### Common Selectors by Site Type
| Site Type | Price | Title | Image |
|-----------|-------|-------|-------|
| E-commerce | `.price`, `[itemprop="price"]` | `h1`, `.product-title` | `.product-image img` |
| News | - | `h1`, `.headline` | `.featured-image` |
| Directory | - | `.listing-title` | `.listing-image` |

## Error Handling

```python
async def safe_scrape(browser, url, selectors):
    try:
        await browser.goto(url)
        await browser.wait_for_load()
        
        # Check for blocks
        if await browser.query('.captcha'):
            return {"error": "CAPTCHA detected", "screenshot": "captcha.png"}
        
        return await extract_data(selectors)
        
    except TimeoutError:
        await browser.screenshot('timeout_error.png')
        return {"error": "Page load timeout"}
    except Exception as e:
        await browser.screenshot('error.png')
        return {"error": str(e)}
```

## Examples

### Example 1: Scrape Product Prices
**Input**: "Scrape all product prices from https://example-store.com/products"

**Execution**:
```python
selectors = {
    "names": ".product-name",
    "prices": ".product-price",
    "urls": ".product-link@href"
}
data = await scrape("https://example-store.com/products", selectors)
```

**Output**:
```json
{
  "success": true,
  "source": "https://example-store.com/products",
  "data": {
    "names": ["Product A", "Product B"],
    "prices": ["$19.99", "$29.99"],
    "urls": ["/products/a", "/products/b"]
  }
}
```

### Example 2: Scrape with Pagination
**Input**: "Get all job listings from https://jobs.example.com, all pages"

**Execution**:
```python
all_jobs = await scrape_all_pages(
    start_url="https://jobs.example.com",
    selectors={"title": ".job-title", "company": ".company-name"},
    next_selector=".pagination-next",
    max_pages=20
)
```

## Rate Limiting Guidelines
- **Minimum delay**: 1 second between pages
- **Max concurrent**: 1 browser at a time
- **Respect robots.txt**: Check before scraping
- **Error backoff**: Double delay on each retry

## Output Formats

### JSON (default)
```json
{"data": [...], "meta": {...}}
```

### CSV
```python
import csv
with open('output.csv', 'w') as f:
    writer = csv.DictWriter(f, fieldnames=data[0].keys())
    writer.writeheader()
    writer.writerows(data)
```

## Quality Checklist
- [ ] Target URL is accessible
- [ ] Selectors tested and working
- [ ] Pagination handled (if applicable)
- [ ] Rate limits respected
- [ ] Data validated before return
- [ ] Screenshot captured for verification
- [ ] Browser session closed

## Integration
- **Works with**: data-analysis, export-csv, documentation
- **Browser**: Required (loads web-tools automatically)

## Changelog
- v1.0.0 (2025-01-24): Initial creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dabugg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
