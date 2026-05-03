---
name: paperize
description: Transform web UI into e-ink optimized interfaces by applying grayscale colors, removing animations, enhancing typography, replacing drag with tap interactions, and adding border emphasis. Use when the user says "paperize", "e-ink friendly", "optimize for e-ink", "low-power display", "Kindle-friendly", or wants to adapt UI for e-readers like Kindle, Kobo, reMarkable, or BOOX. Use when this capability is needed.
metadata:
  author: myprototypewhat
---

# Paperize

> E-ink & Low-Power Device CSS Optimizer

Transform any web UI into an e-ink masterpiece by applying five optimization rules designed for grayscale, low-refresh-rate displays.

## E-ink Constraints

1. **16-level grayscale only** — No true colors; shadows render as dirty blobs
2. **Extremely low refresh rate** — Animations cause painful flickering
3. **Lower pixel density** — Thin fonts become unreadable blurs

## The Five Laws

### A. Grayscale Rule

Convert all colors to grayscale using luminance formula: `gray = 0.299*R + 0.587*G + 0.114*B`

**Operations:**
- Backgrounds → `white` / `gray-50`
- Text → `black` / `gray-900`
- Secondary text → `gray-700` / `gray-600`
- Muted text → `gray-500`
- Remove ALL shadows (`box-shadow`, `text-shadow`)
- Remove ALL gradients → replace with solid background

**Tailwind:**
| Before | After |
|--------|-------|
| `bg-blue-500` | `bg-gray-600` |
| `text-red-600` | `text-black` |
| `shadow-lg` | *(remove)* |
| `bg-gradient-to-r` | `bg-white` |

---

### B. No-Motion Rule

Remove ALL animations and transitions. E-ink refresh is measured in hundreds of milliseconds.

**Operations:**
- Remove `transition`, `animation`, `@keyframes`
- Replace scroll layouts with pagination (Previous/Next buttons)
- Replace `.gif` → static `.png` or `.jpg`
- Replace loaders → static "Loading..." text

**Tailwind:**
| Before | After |
|--------|-------|
| `transition-all` | *(remove)* |
| `duration-300` | *(remove)* |
| `animate-spin` | *(remove)* |
| `animate-pulse` | *(remove)* |
| `hover:scale-105` | *(remove)* |

**Component Replacements:**
| Before | After |
|--------|-------|
| `<InfiniteScroll>` | `<Pagination>` |
| `framer-motion` | Instant state changes |
| `react-spring` | Remove animations |

---

### C. High-Contrast Typography

Increase font weight and spacing for low pixel density displays.

**Operations:**

```css
/* Font weights */
body { font-weight: 500; }       /* Regular text: medium */
h1, h2, h3 { font-weight: 700; } /* Headings: bold */
small { font-weight: 600; }      /* Small text needs MORE weight */

/* Sans-serif fonts only */
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", 
             Roboto, "Helvetica Neue", Arial, sans-serif;

/* Spacing */
body { letter-spacing: 0.02em; line-height: 1.6; }
small { letter-spacing: 0.03em; }

/* Minimum sizes */
body { font-size: 16px; }   /* Never below 16px */
small { font-size: 14px; }  /* Absolute minimum */
```

**Tailwind:**
| Before | After |
|--------|-------|
| `font-light` | `font-medium` |
| `font-thin` | `font-semibold` |
| `tracking-tight` | `tracking-wide` |
| `text-xs` | `text-sm` |
| `leading-tight` | `leading-relaxed` |

---

### D. Tap Over Drag

Replace drag interactions with tap-based alternatives. E-ink's slow refresh makes dragging unusable.

**Detection Patterns:**
- Libraries: `react-beautiful-dnd`, `@dnd-kit/core`, `react-dnd`, `sortablejs`
- Attributes: `draggable="true"`
- Handlers: `onDragStart`, `onDragEnd`, `onDragOver`
- Hooks: `useDrag`, `useDrop`
- CSS: `cursor: grab`, `cursor: grabbing`

**Refactoring:**

1. **Drag-to-reorder → Tap-to-select-and-move:**
   ```jsx
   /* Before */
   <DndContext onDragEnd={handleDragEnd}>
     <SortableItem />
   </DndContext>
   
   /* After */
   <SelectableItem onClick={handleSelect} />
   <MoveTargetSlot onClick={handleMoveTo} />
   ```

2. **Sliders → Steppers:**
   ```jsx
   /* Before */
   <input type="range" min="0" max="100" />
   
   /* After */
   <button onClick={() => setValue(v => v - 10)}>−</button>
   <span>{value}</span>
   <button onClick={() => setValue(v => v + 10)}>+</button>
   ```

3. **Carousels → Grid with pagination:**
   ```jsx
   /* Before */
   <Carousel items={items} />
   
   /* After */
   <Grid items={currentPageItems} />
   <PageControls current={page} total={totalPages} />
   ```

---

### E. Border Emphasis

Without color differentiation, visual hierarchy must come from explicit borders.

**Operations:**

1. Add borders to containers that relied on color/shadow for separation
2. Use border weight for visual hierarchy:
   - Primary: `border-2 border-black`
   - Secondary: `border border-gray-600`
   - Subtle: `border border-gray-300`
3. Add dividers between list items: `divide-y divide-gray-300`
4. Increase padding for breathing room: `p-6` instead of `p-4`

**Tailwind:**
| Before | After |
|--------|-------|
| `bg-blue-50` (colored card) | `bg-white border border-black` |
| `divide-gray-200` | `divide-black` |
| `ring-2 ring-blue-500` | `border-2 border-black` |

---

## Output Format

When paperizing a component, provide:

### 1. Analysis Summary
Categorize detected issues by rule (A-E).

### 2. Transformed Code
Complete CSS/component with before/after annotations.

### 3. Recommendations
- Layout restructuring (scroll → pagination)
- Component replacements (drag → tap)
- Image format changes (gif → static)

---

## Example

**Input:** "Paperize this card component"

```jsx
<div className="bg-gradient-to-r from-purple-500 to-pink-500 
                shadow-xl rounded-lg p-4 
                transition-all duration-300 hover:scale-105">
  <h2 className="text-white font-light">Title</h2>
  <p className="text-purple-100 text-xs">Description</p>
</div>
```

**Output:**

```jsx
<div className="bg-white border-2 border-black rounded-lg p-6">
  <h2 className="text-black font-bold tracking-wide">Title</h2>
  <p className="text-gray-700 text-sm font-medium leading-relaxed">
    Description
  </p>
</div>
```

**Changes Applied:**
- ✅ Rule A: Removed gradient → white background
- ✅ Rule A: Removed shadow
- ✅ Rule B: Removed transition and hover scale
- ✅ Rule C: Increased font weight, letter-spacing
- ✅ Rule C: Increased minimum font size (xs → sm)
- ✅ Rule E: Added 2px black border

---

## Detection & Conditional Application

The transformations should only apply when the user is on an e-ink device or explicitly opts in. Here are detection strategies:

### Strategy 1: CSS Media Queries (Limited)

```css
/* Monochrome detection - unreliable on actual e-ink browsers */
@media (monochrome) {
  /* E-ink styles */
}

/* Accessibility preferences - more reliable */
@media (prefers-reduced-motion: reduce) {
  * { animation: none !important; transition: none !important; }
}

@media (prefers-contrast: more) {
  /* High contrast styles */
}
```

**Limitation:** The `monochrome` media query often returns `false` on actual e-ink devices due to browser bugs. Not recommended as sole detection method.

### Strategy 2: JavaScript Detection

```javascript
function detectEink() {
  // Color depth check (e-ink typically 4-16 bit vs 24-32 bit for normal)
  const isLowColorDepth = screen.colorDepth <= 16;
  
  // User-agent patterns for common e-ink devices
  const einkAgents = /Kindle|BOOX|reMarkable|Kobo|PocketBook/i;
  const isEinkUserAgent = einkAgents.test(navigator.userAgent);
  
  // Reduced motion preference (common on e-ink)
  const prefersReducedMotion = window.matchMedia(
    '(prefers-reduced-motion: reduce)'
  ).matches;
  
  return isEinkUserAgent || (isLowColorDepth && prefersReducedMotion);
}

// Apply paper mode class to document
if (detectEink()) {
  document.documentElement.classList.add('paper-mode');
}
```

### Strategy 3: User Toggle (Recommended)

The most reliable approach is letting users opt in:

```jsx
// React example
function PaperModeToggle() {
  const [paperMode, setPaperMode] = useState(() => {
    return localStorage.getItem('paperMode') === 'true';
  });

  useEffect(() => {
    document.documentElement.classList.toggle('paper-mode', paperMode);
    localStorage.setItem('paperMode', paperMode);
  }, [paperMode]);

  return (
    <button onClick={() => setPaperMode(!paperMode)}>
      {paperMode ? '📖 Paper Mode' : '🖥️ Normal Mode'}
    </button>
  );
}
```

### Conditional Styles with Tailwind

Use the `paper-mode` class as a variant scope:

```css
/* In your CSS */
.paper-mode {
  /* Rule A: Grayscale filter for all images */
  img, video, svg { filter: grayscale(1); }
  
  /* Rule B: Disable all motion */
  *, *::before, *::after {
    animation: none !important;
    transition: none !important;
  }
}

/* Or use Tailwind's group variant */
.paper-mode .text-blue-500 { @apply text-gray-700; }
.paper-mode .bg-gradient-to-r { @apply bg-white; }
.paper-mode .shadow-lg { @apply shadow-none; }
```

### Tailwind Plugin for Paper Mode

```javascript
// tailwind.config.js
module.exports = {
  plugins: [
    function({ addVariant }) {
      addVariant('paper', '.paper-mode &');
    }
  ]
}
```

Then use in components:

```jsx
<div className="bg-blue-500 paper:bg-white paper:border paper:border-black">
  <p className="text-white paper:text-black">
    Adapts to paper mode
  </p>
</div>
```

### Detection Summary

| Method | Reliability | Use Case |
|--------|-------------|----------|
| `@media (monochrome)` | ❌ Low | Avoid as primary detection |
| `screen.colorDepth` | ⚠️ Medium | Supplementary check |
| User-agent sniffing | ⚠️ Medium | Known device targeting |
| `prefers-reduced-motion` | ✅ High | Animation disabling |
| User toggle | ✅ High | Recommended primary method |

**Recommended pattern:** Combine auto-detection with a manual toggle, defaulting to auto-detect but allowing user override.

---

## Additional Resources

- For Tailwind preset configuration, see [tailwind-preset.md](tailwind-preset.md)

## Target Devices

- **E-readers:** Kindle, Kobo, Nook
- **Tablets:** reMarkable, BOOX, Supernote
- **Smartwatches:** E-ink displays
- **IoT:** Low-power dashboard displays
- **Accessibility:** Users with photosensitivity

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/myprototypewhat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
