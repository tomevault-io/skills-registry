---
name: web-scraping-automation
description: Browser automation and web scraping templates using Playwright. Includes data extraction, screenshot capture, form automation, and scheduled monitoring tasks. Use when this capability is needed.
metadata:
  author: dannyai1283-sys
---

# Web Scraping Automation

Automated browser-based data extraction and interaction using Playwright. Perfect for monitoring websites, extracting data, and automating web workflows.

## When to Use

- Extracting data from websites without APIs
- Monitoring website changes
- Automating form submissions
- Taking scheduled screenshots
- Testing web applications
- Price monitoring and alerting
- Content aggregation

## Quick Start

```bash
# Setup scraping environment
./setup.sh

# Run a scraper
./scrape.sh run examples/hacker-news.js

# Schedule recurring scrape
./scrape.sh schedule --url "https://example.com" --every 1h

# Take screenshot
./scrape.sh screenshot https://example.com page.png
```

## Installation

### Prerequisites

```bash
# Install Playwright
npm init -y
npm install playwright
npx playwright install chromium

# Or use Docker
docker run -it --rm mcr.microsoft.com/playwright:v1.40.0-jammy
```

## Basic Scraping

### Simple Page Scraper

```javascript
// scrapers/basic.js
const { chromium } = require('playwright');

async function scrape(url) {
    const browser = await chromium.launch({ headless: true });
    const context = await browser.newContext();
    const page = await context.newPage();
    
    await page.goto(url);
    
    // Extract data
    const data = await page.evaluate(() => {
        return {
            title: document.title,
            headings: Array.from(document.querySelectorAll('h1, h2, h3'))
                .map(h => h.textContent.trim()),
            links: Array.from(document.querySelectorAll('a[href]'))
                .map(a => ({ text: a.textContent, href: a.href }))
                .slice(0, 10)
        };
    });
    
    await browser.close();
    return data;
}

scrape('https://example.com')
    .then(data => console.log(JSON.stringify(data, null, 2)));
```

### Run the Scraper

```bash
node scrapers/basic.js
```

## Advanced Scraping Patterns

### Login and Session Management

```javascript
// scrapers/authenticated.js
const { chromium } = require('playwright');

class AuthenticatedScraper {
    constructor() {
        this.browser = null;
        this.context = null;
        this.page = null;
    }
    
    async init() {
        this.browser = await chromium.launch({ headless: true });
        this.context = await this.browser.newContext();
        this.page = await this.context.newPage();
    }
    
    async login(credentials) {
        await this.page.goto('https://example.com/login');
        
        // Fill login form
        await this.page.fill('input[name="email"]', credentials.email);
        await this.page.fill('input[name="password"]', credentials.password);
        
        // Submit and wait for navigation
        await Promise.all([
            this.page.waitForNavigation(),
            this.page.click('button[type="submit"]')
        ]);
        
        // Verify login success
        const loggedIn = await this.page.locator('.user-profile').isVisible();
        if (!loggedIn) throw new Error('Login failed');
        
        // Save session
        await this.context.storageState({ path: 'auth.json' });
    }
    
    async scrapeDashboard() {
        await this.page.goto('https://example.com/dashboard');
        
        return await this.page.evaluate(() => ({
            stats: document.querySelector('.stats')?.textContent,
            notifications: Array.from(document.querySelectorAll('.notification'))
                .map(n => n.textContent.trim())
        }));
    }
    
    async close() {
        await this.browser.close();
    }
}

// Usage
(async () => {
    const scraper = new AuthenticatedScraper();
    await scraper.init();
    await scraper.login({ email: 'user@example.com', password: 'secret' });
    const data = await scraper.scrapeDashboard();
    console.log(data);
    await scraper.close();
})();
```

### Pagination Handling

```javascript
// scrapers/paginated.js
async function scrapePaginated(url) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    const allItems = [];
    let currentPage = 1;
    let hasNextPage = true;
    
    await page.goto(url);
    
    while (hasNextPage && currentPage <= 10) {
        console.log(`Scraping page ${currentPage}...`);
        
        // Extract items from current page
        const items = await page.$$eval('.item', items =>
            items.map(item => ({
                title: item.querySelector('.title')?.textContent,
                price: item.querySelector('.price')?.textContent,
                link: item.querySelector('a')?.href
            }))
        );
        
        allItems.push(...items);
        
        // Check for next page
        const nextButton = await page.$('.pagination .next:not(.disabled)');
        hasNextPage = !!nextButton;
        
        if (hasNextPage) {
            await Promise.all([
                page.waitForNavigation(),
                nextButton.click()
            ]);
            currentPage++;
            
            // Be nice to the server
            await page.waitForTimeout(1000);
        }
    }
    
    await browser.close();
    return allItems;
}
```

### Infinite Scroll

```javascript
// scrapers/infinite-scroll.js
async function scrapeInfiniteScroll(url) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    await page.goto(url);
    
    // Scroll until no new content loads
    let previousHeight = 0;
    let currentHeight = await page.evaluate(() => document.body.scrollHeight);
    
    while (previousHeight !== currentHeight) {
        previousHeight = currentHeight;
        
        // Scroll to bottom
        await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));
        
        // Wait for content to load
        await page.waitForTimeout(2000);
        
        currentHeight = await page.evaluate(() => document.body.scrollHeight);
    }
    
    // Now extract all data
    const data = await page.evaluate(() =>
        Array.from(document.querySelectorAll('.item')).map(item => ({
            title: item.querySelector('.title')?.textContent,
            content: item.querySelector('.content')?.textContent
        }))
    );
    
    await browser.close();
    return data;
}
```

## Data Extraction Utilities

### Table Extraction

```javascript
// utils/table-extractor.js
async function extractTable(page, selector) {
    return await page.$eval(selector, table => {
        const headers = Array.from(table.querySelectorAll('th'))
            .map(th => th.textContent.trim());
        
        const rows = Array.from(table.querySelectorAll('tbody tr'))
            .map(row => {
                const cells = Array.from(row.querySelectorAll('td'));
                const rowData = {};
                headers.forEach((header, index) => {
                    rowData[header] = cells[index]?.textContent.trim();
                });
                return rowData;
            });
        
        return { headers, rows };
    });
}
```

### JSON-LD Extraction

```javascript
// utils/jsonld-extractor.js
async function extractJsonLd(page) {
    return await page.evaluate(() => {
        const scripts = document.querySelectorAll('script[type="application/ld+json"]');
        return Array.from(scripts).map(script => {
            try {
                return JSON.parse(script.textContent);
            } catch (e) {
                return null;
            }
        }).filter(Boolean);
    });
}
```

## Monitoring and Alerting

### Change Detection

```javascript
// monitors/change-detector.js
const fs = require('fs').promises;
const crypto = require('crypto');

class ChangeDetector {
    constructor(storagePath = './.monitor-state.json') {
        this.storagePath = storagePath;
    }
    
    async hasChanged(url, content) {
        const hash = crypto.createHash('md5').update(content).digest('hex');
        
        let state = {};
        try {
            const data = await fs.readFile(this.storagePath, 'utf8');
            state = JSON.parse(data);
        } catch (e) {
            // No state file yet
        }
        
        const previousHash = state[url];
        const changed = previousHash !== hash;
        
        if (changed) {
            state[url] = hash;
            await fs.writeFile(this.storagePath, JSON.stringify(state, null, 2));
        }
        
        return changed;
    }
}

// Usage in scraper
const detector = new ChangeDetector();
const content = await page.content();
if (await detector.hasChanged(url, content)) {
    console.log('Page has changed!');
    // Send notification, etc.
}
```

### Price Monitor

```javascript
// monitors/price-monitor.js
async function monitorPrice(url, selector, threshold) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    await page.goto(url);
    
    const priceText = await page.locator(selector).textContent();
    const price = parseFloat(priceText.replace(/[^0-9.]/g, ''));
    
    await browser.close();
    
    if (price <= threshold) {
        return {
            alert: true,
            message: `Price dropped to $${price}! (threshold: $${threshold})`,
            url
        };
    }
    
    return { alert: false, price };
}
```

## Screenshot Automation

### Full Page Screenshots

```javascript
// screenshots/full-page.js
async function captureFullPage(url, outputPath) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    await page.goto(url, { waitUntil: 'networkidle' });
    
    // Handle cookie banners (example)
    const cookieBanner = await page.$('.cookie-banner');
    if (cookieBanner) {
        await cookieBanner.evaluate(el => el.remove());
    }
    
    await page.screenshot({
        path: outputPath,
        fullPage: true
    });
    
    await browser.close();
    console.log(`Screenshot saved to ${outputPath}`);
}
```

### Element Screenshots

```javascript
// screenshots/element.js
async function captureElement(url, selector, outputPath) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    await page.goto(url);
    
    const element = await page.locator(selector);
    await element.screenshot({ path: outputPath });
    
    await browser.close();
}
```

### Before/After Comparison

```javascript
// screenshots/compare.js
const { chromium } = require('playwright');
const { expect } = require('@playwright/test');

async function compareScreenshots(url, baselinePath, outputPath) {
    const browser = await chromium.launch();
    const page = await browser.newPage();
    
    await page.goto(url);
    
    // Take new screenshot
    await page.screenshot({ path: outputPath });
    
    // Compare (requires pixelmatch or similar)
    // Implementation depends on comparison library
    
    await browser.close();
}
```

## Scheduling and Automation

### Cron-Based Scraping

```bash
# Add to crontab
*/30 * * * * cd /path/to/scraper && node scrapers/monitor.js >> logs/scraper.log 2>&1
```

### Node Scheduler

```javascript
// scheduler.js
const cron = require('node-cron');

// Run every hour
cron.schedule('0 * * * *', async () => {
    console.log('Running scheduled scrape...');
    await runScraper();
});

// Run every 5 minutes during business hours
cron.schedule('*/5 9-17 * * 1-5', async () => {
    console.log('Running business hours check...');
    await checkPrices();
});
```

## Data Storage

### JSON Export

```javascript
// storage/json-exporter.js
const fs = require('fs').promises;

async function exportToJson(data, filename) {
    const timestamp = new Date().toISOString().split('T')[0];
    const filepath = `./data/${filename}-${timestamp}.json`;
    
    await fs.mkdir('./data', { recursive: true });
    await fs.writeFile(filepath, JSON.stringify(data, null, 2));
    
    return filepath;
}
```

### CSV Export

```javascript
// storage/csv-exporter.js
const fs = require('fs');
const { Parser } = require('json2csv');

function exportToCsv(data, filename) {
    const parser = new Parser();
    const csv = parser.parse(data);
    
    fs.writeFileSync(`./data/${filename}.csv`, csv);
}
```

### Database Storage

```javascript
// storage/db-storage.js
const { Pool } = require('pg');

const pool = new Pool({
    connectionString: process.env.DATABASE_URL
});

async function saveToDatabase(table, data) {
    const keys = Object.keys(data);
    const values = Object.values(data);
    const placeholders = values.map((_, i) => `$${i + 1}`).join(',');
    
    const query = `
        INSERT INTO ${table} (${keys.join(',')})
        VALUES (${placeholders})
        ON CONFLICT DO NOTHING
    `;
    
    await pool.query(query, values);
}
```

## Error Handling and Resilience

### Retry Logic

```javascript
// utils/retry.js
async function withRetry(fn, maxRetries = 3, delay = 1000) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
            return await fn();
        } catch (error) {
            if (attempt === maxRetries) throw error;
            console.log(`Attempt ${attempt} failed, retrying in ${delay}ms...`);
            await new Promise(r => setTimeout(r, delay));
            delay *= 2; // Exponential backoff
        }
    }
}

// Usage
const data = await withRetry(() => scrape(url), 5);
```

### Proxy Rotation

```javascript
// utils/proxy.js
const proxies = [
    'http://proxy1:8080',
    'http://proxy2:8080',
    // ...
];

async function scrapeWithProxy(url) {
    const proxy = proxies[Math.floor(Math.random() * proxies.length)];
    
    const browser = await chromium.launch({
        proxy: { server: proxy }
    });
    
    // ... rest of scraping logic
}
```

## Best Practices

1. **Respect robots.txt** - Check and follow website rules
2. **Rate limiting** - Add delays between requests
3. **User-Agent** - Use descriptive user agent string
4. **Error handling** - Handle network failures gracefully
5. **Data validation** - Validate extracted data before storage
6. **Privacy** - Don't scrape personal data without consent
7. **Terms of Service** - Comply with website ToS

## Ethical Scraping

```javascript
// utils/ethical.js
const robotsParser = require('robots-parser');

async function isAllowed(url) {
    const robotsUrl = new URL('/robots.txt', url).toString();
    const response = await fetch(robotsUrl);
    const robotsTxt = await response.text();
    const robots = robotsParser(robotsUrl, robotsTxt);
    
    return robots.isAllowed(url, 'MyBot/1.0');
}

// Add respectful delays
async function respectfulDelay() {
    await new Promise(r => setTimeout(r, 1000 + Math.random() * 2000));
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannyai1283-sys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
