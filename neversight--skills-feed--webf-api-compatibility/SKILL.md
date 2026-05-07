---
name: webf-api-compatibility
description: Check Web API and CSS feature compatibility in WebF - determine what JavaScript APIs, DOM methods, CSS properties, and layout modes are supported. Use when planning features, debugging why APIs don't work, or finding alternatives for unsupported features like IndexedDB, WebGL, float layout, or CSS Grid. Use when this capability is needed.
metadata:
  author: neversight
---

# WebF API & CSS Compatibility

> **Note**: WebF development is nearly identical to web development - you use the same tools (Vite, npm, Vitest), same frameworks (React, Vue, Svelte), and same deployment services (Vercel, Netlify). This skill covers **one of the 3 key differences**: checking API and CSS compatibility before implementation. The other two differences are async rendering and routing.

**WebF is NOT a browser** - it's a Flutter application runtime that implements W3C/WHATWG web standards. This means some browser APIs are not available, and some CSS features work differently.

This skill helps you quickly check what's supported and find alternatives for unsupported features.

## Quick Compatibility Check

When asked about a specific API or CSS feature, I will:

1. Check if it's in the supported list
2. Provide the compatibility status (✅ Supported, ⏳ Coming Soon, ❌ Not Supported)
3. Explain why it's not supported (if applicable)
4. Suggest alternatives or workarounds

## JavaScript & Web APIs

### ✅ Fully Supported

#### Timers & Animation
- `setTimeout()`, `clearTimeout()`
- `setInterval()`, `clearInterval()`
- `requestAnimationFrame()`, `cancelAnimationFrame()`

#### Networking
- `fetch()` - Full support with async/await
- `XMLHttpRequest` - For legacy code
- `WebSocket` - Real-time communication
- `URL` and `URLSearchParams` - URL manipulation

#### Storage
- `localStorage` - Persistent key-value storage
- `sessionStorage` - Session-only storage

#### Graphics
- **Canvas 2D** - Full 2D canvas API
- **SVG** - SVG element rendering

#### DOM APIs
- `document`, `window`, `navigator`
- `querySelector()`, `querySelectorAll()`
- `addEventListener()`, `removeEventListener()`
- `createElement()`, `appendChild()`, etc.
- `MutationObserver` - Watch DOM changes
- **Custom Elements** - Define custom HTML elements

#### Events
- `click` - Enabled by default
- Other events via `FlutterGestureDetector` (double-tap, long-press, etc.)

### ⏳ Coming Soon

- **CSS Grid** - Planned for future release
- **Tailwind CSS v4** - Planned for 2026

### ❌ NOT Supported

#### Storage
- **IndexedDB** - Use native plugin instead
  - Alternative: `sqflite`, `hive`, or custom plugin

#### Graphics
- **WebGL** - Not available, no alternative
- **Web Animations API** (JavaScript) - CSS animations work, but not the JS API

#### Workers
- **Web Workers** - Not needed
  - Why: JavaScript already runs on dedicated thread in WebF
  - No performance benefit from workers

#### DOM
- **Shadow DOM** - Not used for component encapsulation
  - Use framework component systems instead (React, Vue, etc.)

#### Observers
- **IntersectionObserver** - Use `onscreen`/`offscreen` events instead

## CSS Compatibility

### ✅ Fully Supported Layout Modes

#### Standard Flow
- `block` - Block-level elements
- `inline` - Inline elements
- `inline-block` - Inline elements with block properties

#### Flexbox (Recommended)
- `display: flex`
- All flex properties (`justify-content`, `align-items`, `flex-direction`, etc.)
- **This is the recommended layout approach**

#### Positioned Layout
- `position: relative`
- `position: absolute`
- `position: fixed`
- `position: sticky`

#### Text & Direction
- RTL (right-to-left) support
- All text alignment and direction properties

### ❌ NOT Supported Layout Modes

#### Float Layout (Legacy)
- `float: left` / `float: right` - **NOT SUPPORTED**
- `clear` - **NOT SUPPORTED**
- **Why**: Legacy layout model, use Flexbox instead

#### Table Layout
- `display: table` - **NOT SUPPORTED**
- `display: table-row`, `display: table-cell` - **NOT SUPPORTED**
- **Why**: Use Flexbox or CSS Grid (when available)

### ⏳ Coming Soon

#### CSS Grid
- `display: grid` - **Planned**
- All grid properties - **Planned**
- **Use Flexbox until Grid is available**

### ✅ Fully Supported CSS Features

#### Colors & Backgrounds
- All color formats (hex, rgb, rgba, hsl, hsla, named)
- `background-color`, `background-image`, `background-size`, etc.
- Linear gradients, radial gradients

#### Borders & Shapes
- `border`, `border-radius`, `border-color`, etc.
- `box-shadow`, `text-shadow`

#### Transforms (Hardware Accelerated)
- `transform: translate()`, `rotate()`, `scale()`, `skew()`
- 2D and 3D transforms
- **Use for smooth animations**

#### Animations & Transitions
- `transition` - All transition properties
- `@keyframes` and `animation`
- **CSS animations are fully supported**

#### Layout & Sizing
- `width`, `height`, `min-width`, `max-width`, etc.
- `margin`, `padding`
- `box-sizing`

#### Responsive Design
- `@media` queries
- Viewport units (`vw`, `vh`, `vmin`, `vmax`)
- **Some advanced units not supported** (`dvh`, `lvh`, `svh`)

#### Advanced CSS
- CSS variables (`--custom-property`)
- Pseudo-classes (`:hover`, `:active`, `:focus`, etc.)
- Pseudo-elements (`::before`, `::after`)
- Filters (`blur`, `brightness`, `contrast`, etc.)
- `z-index` and stacking contexts

### ⚠️ Partially Supported

#### Tailwind CSS
- **v3** - ✅ Supported (some utilities may not work if they use unsupported features)
- **v4** - ❌ Not yet supported (planned for 2026)

### ❌ NOT Supported CSS Features

- **`backdrop-filter`** - Not available
- **Advanced viewport units** - `dvh`, `lvh`, `svh`

## Architecture Differences

Understanding why some features aren't available:

| Aspect | Browser | WebF |
|--------|---------|------|
| Runtime | V8 / SpiderMonkey | QuickJS (ES6+) |
| DOM | Blink / Gecko | Custom C++ + Dart |
| Layout | Browser engine | Flutter rendering |
| Purpose | General web browsing | App runtime |

**Key Insight**: WebF implements core web standards for building apps, not for web browsing.

## Finding Alternatives

### For IndexedDB → Use Native Storage

```javascript
// ❌ IndexedDB not available
// const db = await openDB('mydb', 1);

// ✅ Option 1: localStorage for simple key-value storage (RECOMMENDED for most cases)
localStorage.setItem('user', JSON.stringify({ name: 'Alice', age: 30 }));
const user = JSON.parse(localStorage.getItem('user'));

// ✅ Option 2: Request custom native plugin from Flutter team
// For complex database needs (SQL, large datasets, queries):
// - Flutter team creates native plugin using sqflite, Hive, or Isar
// - Plugin exposed to JavaScript via WebF module system
// - See: https://openwebf.com/en/docs/add-webf-to-flutter/bridge-modules

// Example of custom storage plugin (created by Flutter team):
// import { AppStorage } from '@yourapp/storage-plugin';
// await AppStorage.save('key', { complex: 'data' });
// const data = await AppStorage.get('key');
```

### For WebGL → No Alternative

```javascript
// ❌ WebGL not available
// const gl = canvas.getContext('webgl');

// ✅ Use Canvas 2D instead (limited graphics)
const ctx = canvas.getContext('2d');

// ✅ Or use Flutter's rendering for complex graphics
// Flutter team can render directly
```

### For Float Layout → Use Flexbox

```css
/* ❌ Float layout not supported */
.sidebar { float: left; width: 200px; }
.content { float: right; width: calc(100% - 200px); }

/* ✅ Use Flexbox instead */
.container { display: flex; }
.sidebar { width: 200px; flex-shrink: 0; }
.content { flex-grow: 1; }
```

### For Table Layout → Use Flexbox

```css
/* ❌ Table layout not supported */
.table { display: table; }
.row { display: table-row; }
.cell { display: table-cell; }

/* ✅ Use Flexbox instead */
.table { display: flex; flex-direction: column; }
.row { display: flex; }
.cell { flex: 1; }
```

### For Native Device Features → Use WebF Plugins

**Check available plugins**: https://openwebf.com/en/native-plugins

WebF provides official npm packages for native features. When you need a native feature:

1. **First, check the available plugins list** at https://openwebf.com/en/native-plugins
2. **Ask the user about their environment** before providing setup instructions
3. **Follow the plugin's installation guide** for their specific environment

#### Step 1: Determine Your Environment

**IMPORTANT**: Setup differs based on your development environment.

**Question**: "Are you testing in WebF Go, or working on a production app?"

**Option 1: Testing in WebF Go** (Most web developers)
- ✅ Just install npm package
- ✅ No additional setup needed
- Example: `npm install @openwebf/webf-share`

**Option 2: Production app with Flutter team**
- ⚠️ Your Flutter developer needs to add the Flutter plugin first
- ⚠️ Once they've done that, you install the npm package
- ⚠️ Coordinate with your Flutter team - give them the plugin documentation

#### Example: Native Share Plugin

**If using WebF Go:**
```bash
# Just install npm package
npm install @openwebf/webf-share
```

**If integrating with Flutter app:**
```bash
# 1. Add Flutter plugin first (in pubspec.yaml)
# See: https://openwebf.com/en/native-plugins/webf-share

# 2. Then install npm package
npm install @openwebf/webf-share
```

**Usage in JavaScript:**
```javascript
import { WebFShare } from '@openwebf/webf-share';

// Check availability first
if (WebFShare.isAvailable()) {
  // Share text
  await WebFShare.shareText({
    text: 'Check this out!',
    url: 'https://example.com',
    title: 'My App'
  });
}

// Or use React hook
import { useWebFShare } from '@openwebf/webf-share';

function ShareButton() {
  const { share, isAvailable } = useWebFShare();

  if (!isAvailable) return null;

  return (
    <button onClick={() => share({ text: 'Hello!' })}>
      Share
    </button>
  );
}
```

#### Finding the Right Plugin

When looking for native features:

1. **Check plugin list**: https://openwebf.com/en/native-plugins
2. **If plugin exists**: Follow its installation guide
3. **If no plugin exists**:
   - For WebF Go users: Feature may not be available
   - For Flutter integration: Create custom plugin using [WebF Module System](/docs/add-webf-to-flutter/bridge-modules)

## How to Check Compatibility

### 1. Check the Compatibility Table

See `reference.md` in this skill for complete compatibility tables.

### 2. Test in WebF Go

```javascript
// Quick compatibility test
if (typeof IndexedDB !== 'undefined') {
  console.log('IndexedDB available');
} else {
  console.log('IndexedDB NOT available - use alternative');
}

// Check for WebF-specific features
if (typeof WebF !== 'undefined') {
  console.log('Running in WebF');
}
```

### 3. Use Feature Detection

```javascript
function checkStorageOptions() {
  const support = {
    localStorage: typeof localStorage !== 'undefined',
    sessionStorage: typeof sessionStorage !== 'undefined',
    indexedDB: typeof indexedDB !== 'undefined'
  };

  console.log('Storage support:', support);
  return support;
}
```

## Common Questions

### "Can I use Tailwind CSS?"

**Yes, but only v3** - v4 is planned for 2026.

```bash
# Install Tailwind v3
npm install -D tailwindcss@^3.0 postcss autoprefixer
```

Some Tailwind utilities may not work if they use unsupported CSS features (like float or table layout).

### "Why no Web Workers?"

JavaScript in WebF already runs on a **dedicated thread**, separate from the Flutter UI thread. Web Workers would provide no performance benefit.

### "Can I use React Query / SWR / Axios?"

**Yes!** All popular libraries that use `fetch()` or `XMLHttpRequest` work perfectly:

- ✅ React Query
- ✅ SWR
- ✅ Axios
- ✅ TanStack Query
- ✅ Apollo Client (if using fetch)

### "What about CSS-in-JS libraries?"

Most work fine as they generate standard CSS:

- ✅ styled-components
- ✅ Emotion
- ✅ CSS Modules
- ✅ Sass/SCSS

### "Can I use CSS Grid?"

**Not yet** - it's coming soon. Use Flexbox for now, which handles most layouts.

## Debugging Compatibility Issues

If a feature isn't working:

1. **Check this compatibility guide** - Is it supported?
2. **Test in browser first** - Does it work in a regular browser?
3. **Check for typos** - Correct API names?
4. **Read error messages** - WebF provides helpful errors
5. **Use feature detection** - Check if API exists before using

```javascript
// Good practice: feature detection
if (typeof fetch !== 'undefined') {
  // Use fetch
} else {
  // Fallback or error message
  console.error('fetch not available');
}
```

## Resources

- **API Compatibility Table**: https://openwebf.com/en/docs/developer-guide/core-concepts#api-compatibility
- **CSS Support Overview**: https://openwebf.com/en/docs/developer-guide/css
- **CSS Layout Support**: https://openwebf.com/en/docs/learn-webf/key-features#css-layout
- **Native Plugins Guide**: https://openwebf.com/en/docs/developer-guide/native-plugins
- **Complete Reference**: See `reference.md` for detailed tables

## Quick Decision Tree

```
Need storage?
├─ Simple key-value → localStorage ✅
├─ Complex database → Native plugin (sqflite/hive) ✅
└─ IndexedDB → ❌ Not available

Need layout?
├─ Flexible layout → Flexbox ✅
├─ Grid layout → Wait for CSS Grid ⏳ or use Flexbox
├─ Float layout → ❌ Use Flexbox instead
└─ Table layout → ❌ Use Flexbox instead

Need graphics?
├─ 2D canvas → Canvas 2D ✅
├─ SVG → SVG ✅
├─ 3D graphics → ❌ WebGL not available
└─ Complex graphics → Flutter rendering ✅

Need networking?
├─ HTTP requests → fetch ✅
├─ Real-time → WebSockets ✅
└─ GraphQL → fetch-based clients ✅

Need native features?
├─ Share → @openwebf/webf-share ✅
├─ Camera → Native plugin ✅
├─ Storage → Native plugin ✅
└─ Custom → Flutter team creates plugin ✅
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
