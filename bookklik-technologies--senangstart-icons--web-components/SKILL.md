---
name: web-components
description: Working with the ss-icon Web Component and ss-loader Use when this capability is needed.
metadata:
  author: bookklik-technologies
---

# Web Components Skill

This skill covers the two JavaScript components that render icons.

## ss-icon Web Component

**Location:** `src/ss-icon.js`

### Usage

```html
<ss-icon icon="check"></ss-icon>
<ss-icon icon="arrow-left" thickness="1.5"></ss-icon>
```

### Attributes

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `icon` | string | - | Icon slug from icons.json |
| `thickness` | number | 2 | Stroke width of the icon |

### How It Works

```javascript
class SSIcon extends HTMLElement {
  // Watch for attribute changes
  static get observedAttributes() {
    return ["icon", "thickness"];
  }

  constructor() {
    super();
    this.attachShadow({ mode: "open" });
  }

  // Re-render when attributes change
  attributeChangedCallback(name, oldValue, newValue) {
    if (name === "icon" || name === "thickness") {
      this.render();
    }
  }

  render() {
    const iconName = this.getAttribute("icon");
    const iconSvg = icons[iconName];
    // Inject SVG with custom thickness into shadow DOM
  }
}

customElements.define("ss-icon", SSIcon);
```

### Styling

The component inherits color from parent:

```css
.my-button {
  color: blue;
}
.my-button ss-icon {
  /* Icon will be blue */
}
```

Override size:

```css
ss-icon {
  font-size: 24px; /* Icons scale with font-size */
}
```

---

## ss-loader (Class-based Icons)

**Location:** `src/ss-loader.js`

### Usage

```html
<i class="ss ss-check"></i>
<i class="ss ss-arrow-left"></i>
```

### How It Works

1. **Initial Load:** On DOMContentLoaded, finds all `i.ss` elements
2. **Extract Icon:** Looks for class starting with `ss-`
3. **Inject SVG:** Inserts SVG content into the element
4. **Mark Loaded:** Adds `data-ss-loaded="true"` to prevent reprocessing
5. **Observe Changes:** MutationObserver watches for new icons added dynamically

```javascript
function replaceIcons() {
  const elements = document.querySelectorAll("i.ss");
  elements.forEach((el) => {
    if (el.dataset.ssLoaded) return; // Skip already processed
    
    const iconClass = classes.find(c => c.startsWith("ss-"));
    const iconName = iconClass.replace("ss-", "");
    const iconSvg = icons[iconName];
    
    if (iconSvg) {
      el.innerHTML = iconSvg;
      el.dataset.ssLoaded = "true";
    }
  });
}

// Watch for dynamically added icons
const observer = new MutationObserver((mutations) => {
  // Check for added nodes or class changes
  if (shouldUpdate) replaceIcons();
});

observer.observe(document.body, {
  childList: true,
  subtree: true,
  attributes: true,
  attributeFilter: ["class"]
});
```

### Styling Class-based Icons

```css
.ss {
  display: inline-block;
  width: 1em;
  height: 1em;
  vertical-align: middle;
}

/* Custom styling */
.ss-check {
  color: green;
  font-size: 2rem;
}
```

---

## CSS-Only Icons (Alternative)

For environments without JavaScript, use the CSS-only approach:

```html
<link rel="stylesheet" href="senangstart.min.css">
<i class="ss ss-check"></i>
```

The CSS uses `mask-image` with data URIs - no JavaScript required.

---

## Extending the Components

### Adding New Attributes to ss-icon

1. Add to `observedAttributes` array
2. Handle in `render()` method
3. Update tests in `tests/ss-icon.test.js`

### Example: Adding a `color` attribute

```javascript
static get observedAttributes() {
  return ["icon", "thickness", "color"];
}

render() {
  const color = this.getAttribute("color") || "currentColor";
  this.shadowRoot.innerHTML = `
    <style>
      :host { color: ${color}; }
      /* ... */
    </style>
    ${finalSvg}
  `;
}
```

### Adding Animation Support

```javascript
render() {
  const animate = this.hasAttribute("animate");
  this.shadowRoot.innerHTML = `
    <style>
      ${animate ? `
        @keyframes spin {
          from { transform: rotate(0deg); }
          to { transform: rotate(360deg); }
        }
        svg { animation: spin 1s linear infinite; }
      ` : ""}
    </style>
    ${finalSvg}
  `;
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bookklik-technologies) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
