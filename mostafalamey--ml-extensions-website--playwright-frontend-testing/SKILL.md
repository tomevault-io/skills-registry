---
name: playwright-frontend-testing
description: Uses Playwright MCP to enable visual testing, user flow validation, and automated testing of the extension website. Use when implementing frontend testing or validating user interactions. Use when this capability is needed.
metadata:
  author: mostafalamey
---

# Playwright Frontend Testing

This skill leverages Playwright MCP to provide comprehensive frontend testing and visual validation for your ML Extensions website.

## Testing Strategy

### User Journey Testing
Test critical paths that lead to conversions:

1. **Homepage → Product Discovery**
2. **Product Page → Purchase Decision**  
3. **Purchase Flow → Completion**
4. **Support → Problem Resolution**

### Key Test Scenarios

#### E-commerce Flow Testing
```javascript
// Test the complete purchase journey
const testPurchaseFlow = async (page) => {
    // Navigate to homepage
    await page.goto('https://your-site.com');
    
    // Check hero section loads
    await page.screenshot({ path: 'homepage-hero.png' });
    
    // Navigate to product page
    await page.click('text=ML Kitchens');
    await page.waitForSelector('.product-hero');
    
    // Verify product information
    await page.screenshot({ path: 'product-page.png' });
    
    // Test Gumroad integration
    await page.click('.gumroad-button');
    await page.waitForSelector('[data-gumroad-overlay]');
    
    // Validate checkout overlay
    await page.screenshot({ path: 'checkout-overlay.png' });
};
```

## Playwright MCP Integration

### Browser Setup and Navigation
```javascript
// Use MCP browser tools for testing
async function setupTestEnvironment() {
    // Install browser if needed
    await mcp_microsoft_pla_browser_install();
    
    // Navigate to your site
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173" // Your Vite dev server
    });
    
    // Take initial snapshot
    await mcp_microsoft_pla_browser_snapshot();
}
```

### Visual Regression Testing
```javascript
async function visualRegressionTest() {
    // Take screenshots of key pages
    const pages = [
        '/', 
        '/extensions/ml-kitchens',
        '/extensions/ml-doors',
        '/support',
        '/about'
    ];
    
    for (const page of pages) {
        await mcp_microsoft_pla_browser_navigate({ url: `http://localhost:5173${page}` });
        await mcp_microsoft_pla_browser_take_screenshot({
            filename: `${page.replace('/', '-') || 'homepage'}.png`,
            fullPage: true
        });
    }
}
```

### Interactive Element Testing
```javascript
async function testInteractiveElements() {
    // Test navigation menu
    await mcp_microsoft_pla_browser_click({
        ref: "nav-products-button",
        element: "Products navigation button"
    });
    
    // Verify dropdown appears
    await mcp_microsoft_pla_browser_snapshot();
    
    // Test product gallery
    await mcp_microsoft_pla_browser_click({
        ref: "gallery-image-1", 
        element: "First gallery image"
    });
    
    // Wait for modal/lightbox
    await mcp_microsoft_pla_browser_wait_for({
        text: "Image Gallery"
    });
    
    // Test modal functionality
    await mcp_microsoft_pla_browser_press_key({ key: "Escape" });
}
```

## Test Scenarios for ML Extensions Website

### Homepage Critical Elements
```javascript
async function testHomepage() {
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173"
    });
    
    // Test hero section
    const snapshot = await mcp_microsoft_pla_browser_snapshot();
    console.log('Homepage loaded:', snapshot);
    
    // Test navigation
    await mcp_microsoft_pla_browser_hover({
        ref: "nav-products",
        element: "Products menu"
    });
    
    // Test CTA buttons
    await mcp_microsoft_pla_browser_click({
        ref: "hero-cta",
        element: "Main call-to-action button"
    });
    
    // Verify navigation worked
    await mcp_microsoft_pla_browser_wait_for({
        text: "Our Extensions"
    });
}
```

### Product Page Validation
```javascript  
async function testProductPage(extensionSlug) {
    await mcp_microsoft_pla_browser_navigate({
        url: `http://localhost:5173/extensions/${extensionSlug}`
    });
    
    // Test image gallery
    await mcp_microsoft_pla_browser_click({
        ref: "gallery-thumbnail-1",
        element: "Product gallery thumbnail"
    });
    
    // Test video player
    await mcp_microsoft_pla_browser_click({
        ref: "demo-video-play",
        element: "Demo video play button"  
    });
    
    // Test Gumroad integration
    await mcp_microsoft_pla_browser_click({
        ref: "purchase-button",
        element: "Purchase extension button"
    });
    
    // Verify Gumroad overlay  
    await mcp_microsoft_pla_browser_wait_for({
        text: "Gumroad"
    });
    
    await mcp_microsoft_pla_browser_take_screenshot({
        filename: `${extensionSlug}-purchase-flow.png`
    });
}
```

### Responsive Design Testing
```javascript
async function testResponsiveDesign() {
    const viewports = [
        { width: 375, height: 667 },  // Mobile
        { width: 768, height: 1024 }, // Tablet  
        { width: 1200, height: 800 }, // Desktop
        { width: 1920, height: 1080 } // Large Desktop
    ];
    
    for (const viewport of viewports) {
        await mcp_microsoft_pla_browser_resize({
            width: viewport.width,
            height: viewport.height
        });
        
        await mcp_microsoft_pla_browser_take_screenshot({
            filename: `responsive-${viewport.width}x${viewport.height}.png`,
            fullPage: true
        });
        
        // Test mobile menu
        if (viewport.width < 768) {
            await mcp_microsoft_pla_browser_click({
                ref: "mobile-menu-toggle",
                element: "Mobile menu hamburger"
            });
            
            await mcp_microsoft_pla_browser_snapshot();
        }
    }
}
```

### Form Testing  
```javascript
async function testContactForm() {
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173/support"
    });
    
    // Fill out contact form
    await mcp_microsoft_pla_browser_fill_form({
        fields: [
            {
                name: "Name field",
                type: "textbox",
                ref: "contact-name",
                value: "Test User"
            },
            {
                name: "Email field", 
                type: "textbox",
                ref: "contact-email",
                value: "test@example.com"
            },
            {
                name: "Message field",
                type: "textbox", 
                ref: "contact-message",
                value: "This is a test message for the contact form."
            }
        ]
    });
    
    // Submit form
    await mcp_microsoft_pla_browser_click({
        ref: "contact-submit",
        element: "Submit contact form"
    });
    
    // Verify success message
    await mcp_microsoft_pla_browser_wait_for({
        text: "Thank you for your message"
    });
}
```

## Performance Testing

### Load Time Measurement
```javascript
async function measurePerformance() {
    const startTime = Date.now();
    
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173"
    });
    
    // Wait for page to be fully loaded
    await mcp_microsoft_pla_browser_wait_for({
        text: "ML Extensions"
    });
    
    const loadTime = Date.now() - startTime;
    console.log(`Page load time: ${loadTime}ms`);
    
    // Check for console errors
    const consoleMessages = await mcp_microsoft_pla_browser_console_messages({
        level: "error"
    });
    
    if (consoleMessages.length > 0) {
        console.error('Console errors found:', consoleMessages);
    }
}
```

### Network Request Monitoring
```javascript
async function monitorNetworkRequests() {
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173"
    });
    
    // Get all network requests
    const networkRequests = await mcp_microsoft_pla_browser_network_requests({
        includeStatic: true,
        filename: "network-requests.json"
    });
    
    // Analyze slow requests
    const slowRequests = networkRequests.filter(req => req.duration > 1000);
    if (slowRequests.length > 0) {
        console.warn('Slow network requests detected:', slowRequests);
    }
}
```

## Accessibility Testing

### Keyboard Navigation
```javascript
async function testKeyboardNavigation() {
    await mcp_microsoft_pla_browser_navigate({
        url: "http://localhost:5173"
    });
    
    // Test tab navigation
    await mcp_microsoft_pla_browser_press_key({ key: "Tab" });
    await mcp_microsoft_pla_browser_press_key({ key: "Tab" });
    await mcp_microsoft_pla_browser_press_key({ key: "Tab" });
    
    // Test Enter key activation
    await mcp_microsoft_pla_browser_press_key({ key: "Enter" });
    
    // Verify focus is visible
    await mcp_microsoft_pla_browser_take_screenshot({
        filename: "keyboard-focus.png"
    });
}
```

### Screen Reader Testing
```javascript
async function testScreenReaderCompatibility() {
    const snapshot = await mcp_microsoft_pla_browser_snapshot();
    
    // Check for proper heading structure
    const headings = snapshot.match(/<h[1-6][^>]*>(.*?)<\/h[1-6]>/gi);
    console.log('Page headings:', headings);
    
    // Verify alt text on images
    const images = snapshot.match(/<img[^>]+alt=["'][^"']*["'][^>]*>/gi);
    console.log('Images with alt text:', images);
}
```

## Automated Test Suite

### Test Runner Configuration
```javascript
// testRunner.js
export class MLExtensionsTestSuite {
    async runFullTestSuite() {
        console.log('🚀 Starting ML Extensions Test Suite...');
        
        try {
            // Setup
            await this.setupTestEnvironment();
            
            // Core functionality tests
            await this.testHomepage();
            await this.testProductPages();
            await this.testPurchaseFlow();
            
            // Performance tests  
            await this.measurePerformance();
            await this.monitorNetworkRequests();
            
            // Accessibility tests
            await this.testKeyboardNavigation();
            
            // Responsive design tests
            await this.testResponsiveDesign();
            
            console.log('✅ All tests completed successfully');
        } catch (error) {
            console.error('❌ Test suite failed:', error);
            throw error;
        }
    }
    
    async generateTestReport() {
        // Collect all screenshots and results
        // Generate HTML report
        // Send notifications if tests fail
    }
}
```

## CI/CD Integration  

### GitHub Actions Workflow
```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests
on: [push, pull_request]

jobs:
  playwright-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application  
        run: npm run build
      
      - name: Start development server
        run: npm run dev &
        
      - name: Run Playwright tests
        run: |
          # Use MCP Playwright tools for testing
          node test-runner.js
          
      - name: Upload test results
        uses: actions/upload-artifact@v3
        with:
          name: playwright-results
          path: test-results/
```

## Testing Checklist

### Pre-Release Testing
- [ ] Homepage loads without errors
- [ ] All product pages render correctly  
- [ ] Navigation menus work on all devices
- [ ] Gumroad integration functions properly
- [ ] Image galleries and videos load
- [ ] Contact forms submit successfully
- [ ] Page load times are under 3 seconds
- [ ] No console errors in browser
- [ ] Responsive design works on mobile/tablet
- [ ] Keyboard navigation is functional
- [ ] All images have alt text
- [ ] Meta tags are properly set

### Ongoing Monitoring  
- [ ] Set up automated daily tests
- [ ] Monitor performance regressions
- [ ] Track conversion funnel metrics
- [ ] Alert on broken purchase flows
- [ ] Validate third-party integrations
- [ ] Check cross-browser compatibility

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mostafalamey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
