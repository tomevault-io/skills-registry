---
name: chrome-devtools
description: Use this skill when working with Chrome DevTools, browser debugging, performance profiling, network analysis, JavaScript debugging, CSS inspection, element inspection, console usage, or any web development debugging workflows.
metadata:
  author: acejou27
---

# Chrome DevTools Skill

Expert guidance for Chrome DevTools - the comprehensive suite of web development and debugging tools built into Chrome.

## When to Use This Skill

This skill should be triggered when:
- Debugging JavaScript code with breakpoints and stepping
- Inspecting and modifying CSS styles and layouts
- Analyzing network requests and responses
- Profiling performance and identifying bottlenecks
- Working with browser storage (cookies, localStorage, sessionStorage)
- Inspecting DOM elements and their properties
- Using the Console API for logging and debugging
- Testing responsive designs and mobile viewports
- Debugging animations and transitions
- Working with Progressive Web Apps (PWAs)
- Using Chrome DevTools Protocol or automation
- Emulating device sensors (geolocation, orientation)
- Analyzing JavaScript heap and memory usage

## Quick Reference

### JavaScript Debugging

**Setting Breakpoints and Inspecting Variables**
```javascript
// Set breakpoints in DevTools Sources panel
// Inspect variable values in the Scope pane
function updateLabel() {
  var addend1 = getNumber1();
  var addend2 = getNumber2();
  var sum = addend1 + addend2;  // Set breakpoint here (line 32)
  label.textContent = addend1 + ' + ' + addend2 + ' = ' + sum;
}
```

### Console API

**Basic Logging and Assertions**
```javascript
// Log messages at different levels
console.log('Informational message');
console.warn('Warning message');
console.error('Error message');
console.debug('Debug message');

// Assertions
const x = 5, y = 3;
console.assert(x < y, {x, y, reason: 'x should be less than y'});
```

**Grouping and Tables**
```javascript
// Group related logs
console.group('User Details');
console.info('Name: John');
console.info('Age: 30');
console.groupEnd();

// Display data as table
const users = [
  { first: 'John', last: 'Doe', age: 30 },
  { first: 'Jane', last: 'Smith', age: 25 }
];
console.table(users);
```

**Timing and Tracing**
```javascript
// Measure execution time
console.time('loop');
for (let i = 0; i < 100000; i++) {
  let square = i ** 2;
}
console.timeEnd('loop');

// Print stack trace
console.trace();
```

### Console Utilities

**Selecting and Inspecting Elements**
```javascript
// Select elements (like jQuery)
$('img');  // First img element
$$('img'); // All img elements

// Select with starting node
let images = $$('img', document.querySelector('.header'));
for (let img of images) {
  console.log(img.src);
}

// XPath selection
$x('//p');           // All <p> elements
$x('//p[contains(., "text")]'); // <p> elements containing "text"
```

**Inspecting Recently Selected Elements**
```javascript
// $0 = most recently selected element in Elements panel
// $1 = second most recent, etc.
$0.textContent;
$1.className;
```

**Monitoring Events**
```javascript
// Get all event listeners on an element
getEventListeners(document);

// Monitor events
monitorEvents(window, 'resize');
unmonitorEvents(window, 'resize');

// Debug function calls
debug(myFunction);  // Breaks when myFunction is called
undebug(myFunction);
```

### CSS Inspection and Debugging

**Grid Layout Debugging**
```css
/* Define a grid layout */
.container {
  display: grid;
  grid-template-columns: 1fr 2fr;
  grid-template-rows: 80px 80px;
  grid-template-areas:
    "left right"
    "middle1 middle2";
}

/* DevTools shows:
 * - Grid badge in Elements panel
 * - Grid overlay with line numbers
 * - Track sizes (1fr - 96.66px, 2fr - 193.32px)
 * - Area names visualization
 */
```

**Animation Debugging**
```css
/* Inspect animations with Animations panel */
.element {
  animation: slideIn 2s ease-in-out infinite;
}

@keyframes slideIn {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(0); }
}

/* DevTools lets you:
 * - Replay animations
 * - Adjust speed (25%, 50%, 100%)
 * - Edit timing and delays
 * - Modify @keyframes live
 */
```

### Network Analysis

**Inspecting Request Headers**
```javascript
// In Network panel, click request > Headers tab
// Or programmatically:
fetch('https://api.example.com/data', {
  headers: {
    'Authorization': 'Bearer token',
    'Content-Type': 'application/json'
  }
})
.then(response => {
  // Inspect timing in Network panel
  // Check response headers
  console.log(response.headers.get('content-type'));
});
```

### Performance Profiling

**Node.js CPU Profiling**
```bash
# Start Node with inspector
node --inspect file-name.js

# Or Deno
deno --inspect file-name.js

# Profile with console API
console.profile('myProfile');
doSomething();
console.profileEnd();
```

### Device Emulation

**Responsive Testing**
```javascript
// Open Device Toolbar (Cmd+Shift+M / Ctrl+Shift+M)
// Dimensions: Set to specific device or custom
// Device Pixel Ratio: Simulate HiDPI displays
// User Agent: Test as mobile/desktop

// Emulate sensors programmatically
// In Sensors panel:
// - Geolocation: Set custom lat/long
// - Orientation: alpha, beta, gamma
// - Touch: Force touch events
```

### Storage Management

**Working with localStorage**
```javascript
// View in Application > Local Storage
localStorage.setItem('user', JSON.stringify({name: 'John', id: 123}));
localStorage.getItem('user');
localStorage.removeItem('user');
localStorage.clear();

// Edit values directly in DevTools UI
// Filter by key/value
// See storage quota usage
```

**Cookie Management**
```javascript
// View/edit in Application > Cookies
document.cookie = "username=John; expires=Fri, 31 Dec 2024 23:59:59 GMT";

// DevTools shows:
// - Name, Value, Domain, Path
// - Expiration, Size, HttpOnly, Secure
// - SameSite attribute
// - Third-party cookie warnings
```

### Live Expressions

**Monitor Values in Real-Time**
```javascript
// Create live expression in Console
// Updates every 250ms

// Track focused element
document.activeElement

// Monitor scroll position
window.scrollY

// Watch variable type
typeof myVariable
```

### Progressive Web Apps

**Service Worker Debugging**
```javascript
// Register service worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js')
    .then(reg => console.log('SW registered', reg))
    .catch(err => console.log('SW error', err));
}

// Debug in Application > Service Workers
// - View lifecycle (install, wait, activate)
// - Update on reload
// - Bypass for network
// - Unregister service workers
```

## Key Concepts

### Breakpoints
Pause code execution to inspect state. Types include:
- **Line-of-code**: Pause at specific line
- **Conditional**: Pause only when condition is true
- **Event listener**: Pause when event fires (click, keydown, etc.)
- **XHR/Fetch**: Pause on network requests
- **Exception**: Pause on caught/uncaught errors
- **DOM**: Pause on element modifications

### Stepping Through Code
- **Step into** (F11): Enter function calls
- **Step over** (F10): Execute current line, skip function internals
- **Step out** (Shift+F11): Exit current function
- **Continue** (F8): Resume until next breakpoint

### Scope and Closures
The Scope pane shows:
- **Local**: Variables in current function
- **Closure**: Variables from outer scopes
- **Global**: Window/global variables
- **Script**: Module-level variables

### Device Pixel Ratio (DPR)
Ratio between physical pixels and CSS pixels. Important for HiDPI displays:
- Standard: 1x
- Retina: 2x or 3x
- Affects image quality and CSS rendering

### Color Spaces
DevTools supports both standard and HD color spaces:
- **Standard**: RGB, HSL, HWB, Hex
- **HD**: Display P3, Rec2020, Lab, LCH, OKLab, OKLCH
- Use Color Picker to convert between spaces

### Cache Storage
Service worker cache for offline functionality:
- Uses Cache API
- Read-only in DevTools
- Opaque responses consume ~7MB minimum (quota padding)
- Clear via Application > Clear Storage

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **ai.md** - AI assistance panel, Gemini integration, prompt handling
- **application.md** - Storage inspection (cookies, localStorage), PWA debugging, service workers
- **console.md** - Console API reference, utilities, debugging JavaScript
- **device.md** - Device emulation, sensors (geolocation, orientation), responsive design
- **elements.md** - DOM inspection, CSS debugging, animations, grid/flexbox layouts, color picker
- **getting_started.md** - Basic debugging workflow, breakpoints, stepping through code
- **network.md** - Network analysis, request inspection, timing, headers
- **other.md** - Additional DevTools features and utilities
- **performance.md** - CPU profiling, performance analysis, Node.js debugging
- **recorder.md** - User flow recording and replay
- **rendering.md** - Rendering performance, paint flashing, layer inspection
- **security.md** - Security issues, HTTPS, mixed content, certificates
- **sources.md** - Sources panel, code editor, workspace, snippets

## Working with This Skill

### For Beginners
Start with:
1. **getting_started.md** - Learn basic debugging workflow
2. **console.md** - Master console logging and utilities
3. **elements.md** - Understand DOM and CSS inspection

### For Intermediate Users
Focus on:
1. **network.md** - Analyze network performance
2. **performance.md** - Profile CPU and memory
3. **application.md** - Debug storage and PWAs
4. **device.md** - Test responsive designs

### For Advanced Users
Explore:
1. **sources.md** - Advanced debugging techniques
2. **rendering.md** - Optimize rendering performance
3. **ai.md** - Use AI assistance for debugging
4. **security.md** - Identify security vulnerabilities

### Navigation Tips
- Use Cmd+P (Mac) / Ctrl+P (Windows) to quickly open files in Sources
- Cmd+Shift+P (Mac) / Ctrl+Shift+P (Windows) opens Command Menu
- Cmd+Shift+C (Mac) / Ctrl+Shift+C (Windows) activates element inspector
- F12 or Cmd+Option+I (Mac) / Ctrl+Shift+I (Windows) toggles DevTools

## Common Workflows

### Debug a JavaScript Error
1. Open Console to see error message and stack trace
2. Click file link to jump to Sources panel
3. Set breakpoint near error location
4. Reload page to trigger breakpoint
5. Use Scope pane to inspect variable values
6. Step through code to find root cause

### Fix CSS Layout Issues
1. Right-click element > Inspect
2. Check Computed tab for final styles
3. Use Grid/Flexbox badges to visualize layout
4. Edit styles live in Styles pane
5. Use Color Picker for color adjustments
6. Check Animations panel for animation issues

### Optimize Network Performance
1. Open Network panel and reload page
2. Filter by resource type (JS, CSS, Images)
3. Check Waterfall for timing issues
4. Inspect large resources
5. Enable throttling to test slow connections
6. Use Coverage tab to find unused code

### Test Mobile Experience
1. Open Device Toolbar (Cmd+Shift+M)
2. Select device or set custom dimensions
3. Test touch events
4. Emulate geolocation in Sensors panel
5. Test orientation changes
6. Capture screenshots for documentation

## Resources

### Official Documentation
- Chrome DevTools Docs: https://developer.chrome.com/docs/devtools/
- DevTools Protocol: https://chromedevtools.github.io/devtools-protocol/

### Quick Tips
- Double-click to edit most values in DevTools
- Right-click almost anywhere for context menus
- Use $ and $$ as shortcuts for querySelector
- Copy values with copy() function in Console
- Clear Console with Ctrl+L or console.clear()

## Notes

- This skill was automatically generated from official Chrome DevTools documentation
- Reference files preserve structure and examples from source docs
- Code examples include language detection for syntax highlighting
- Quick reference patterns are extracted from real-world usage

## Updating

To refresh this skill with updated documentation:
1. Re-run the scraper with the same configuration
2. Review and enhance the Quick Reference section
3. Update any changed APIs or features

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acejou27) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
