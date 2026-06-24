---
name: theme-factory
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Theme Factory

Toolkit for applying visual themes to slides, documents, reports, and HTML pages.

## Pre-set Themes

### 1. Professional Dark
```css
:root {
  --bg-primary: #1a1a2e;
  --bg-secondary: #16213e;
  --text-primary: #eaeaea;
  --text-secondary: #a0a0a0;
  --accent: #e94560;
  --accent-secondary: #0f3460;
  --font-heading: 'Inter', sans-serif;
  --font-body: 'Inter', sans-serif;
}
```

### 2. Clean Light
```css
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f8f9fa;
  --text-primary: #212529;
  --text-secondary: #6c757d;
  --accent: #0066cc;
  --accent-secondary: #e9ecef;
  --font-heading: 'Helvetica Neue', sans-serif;
  --font-body: 'Georgia', serif;
}
```

### 3. Nature
```css
:root {
  --bg-primary: #f0f4e8;
  --bg-secondary: #e8f0e3;
  --text-primary: #2d3a24;
  --text-secondary: #5a6b4a;
  --accent: #4a7c59;
  --accent-secondary: #a8c69f;
  --font-heading: 'Playfair Display', serif;
  --font-body: 'Source Sans Pro', sans-serif;
}
```

### 4. Tech Startup
```css
:root {
  --bg-primary: #0a0a0a;
  --bg-secondary: #151515;
  --text-primary: #ffffff;
  --text-secondary: #888888;
  --accent: #00ff88;
  --accent-secondary: #003322;
  --font-heading: 'Space Grotesk', sans-serif;
  --font-body: 'IBM Plex Mono', monospace;
}
```

### 5. Corporate Blue
```css
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f0f4f8;
  --text-primary: #1a365d;
  --text-secondary: #4a5568;
  --accent: #2b6cb0;
  --accent-secondary: #bee3f8;
  --font-heading: 'Roboto', sans-serif;
  --font-body: 'Open Sans', sans-serif;
}
```

### 6. Warm Minimal
```css
:root {
  --bg-primary: #faf8f5;
  --bg-secondary: #f5f0e8;
  --text-primary: #2d2a26;
  --text-secondary: #6b6560;
  --accent: #c17f59;
  --accent-secondary: #e8d5c4;
  --font-heading: 'Lora', serif;
  --font-body: 'Lato', sans-serif;
}
```

### 7. Ocean
```css
:root {
  --bg-primary: #0c1821;
  --bg-secondary: #1b2838;
  --text-primary: #ccd6e0;
  --text-secondary: #8899a6;
  --accent: #00b4d8;
  --accent-secondary: #023e4f;
  --font-heading: 'Poppins', sans-serif;
  --font-body: 'Nunito', sans-serif;
}
```

### 8. Sunset
```css
:root {
  --bg-primary: #2b1b35;
  --bg-secondary: #3d2a4d;
  --text-primary: #f8f0e3;
  --text-secondary: #c9b8a8;
  --accent: #ff6b6b;
  --accent-secondary: #feca57;
  --font-heading: 'Montserrat', sans-serif;
  --font-body: 'Raleway', sans-serif;
}
```

### 9. Minimal Mono
```css
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #fafafa;
  --text-primary: #111111;
  --text-secondary: #666666;
  --accent: #111111;
  --accent-secondary: #eeeeee;
  --font-heading: 'JetBrains Mono', monospace;
  --font-body: 'JetBrains Mono', monospace;
}
```

### 10. Bold Creative
```css
:root {
  --bg-primary: #fef3c7;
  --bg-secondary: #fde68a;
  --text-primary: #1f2937;
  --text-secondary: #4b5563;
  --accent: #8b5cf6;
  --accent-secondary: #ec4899;
  --font-heading: 'Archivo Black', sans-serif;
  --font-body: 'DM Sans', sans-serif;
}
```

## Applying Themes

### HTML/CSS
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    /* Paste theme variables here */
    :root { ... }

    body {
      background: var(--bg-primary);
      color: var(--text-primary);
      font-family: var(--font-body);
    }

    h1, h2, h3 {
      font-family: var(--font-heading);
      color: var(--text-primary);
    }

    .card {
      background: var(--bg-secondary);
      border-left: 4px solid var(--accent);
    }

    a {
      color: var(--accent);
    }

    .button {
      background: var(--accent);
      color: var(--bg-primary);
    }
  </style>
</head>
```

### React/Tailwind
```jsx
const theme = {
  colors: {
    bgPrimary: '#1a1a2e',
    bgSecondary: '#16213e',
    textPrimary: '#eaeaea',
    textSecondary: '#a0a0a0',
    accent: '#e94560',
  }
};

// Usage in Tailwind
<div className="bg-[#1a1a2e] text-[#eaeaea]">
  <h1 className="font-bold">Title</h1>
  <button className="bg-[#e94560]">Click</button>
</div>
```

### Python (for documents/presentations)
```python
theme = {
    'bg_primary': '#1a1a2e',
    'bg_secondary': '#16213e',
    'text_primary': '#eaeaea',
    'accent': '#e94560',
    'font_heading': 'Arial',
    'font_body': 'Calibri',
}

# Apply to python-pptx
from pptx.util import Pt
from pptx.dml.color import RgbColor

def hex_to_rgb(hex_color):
    hex_color = hex_color.lstrip('#')
    return RgbColor(
        int(hex_color[0:2], 16),
        int(hex_color[2:4], 16),
        int(hex_color[4:6], 16)
    )

shape.text_frame.paragraphs[0].font.color.rgb = hex_to_rgb(theme['accent'])
```

## Creating Custom Themes

### Color Selection
1. **Start with one accent color**
2. **Choose complementary background** (light or dark)
3. **Set text colors** with good contrast (4.5:1 ratio minimum)
4. **Add secondary colors** for hover states, borders

### Font Pairing Rules
| Heading Style | Body Style | Vibe |
|--------------|------------|------|
| Sans-serif (bold) | Sans-serif (regular) | Modern, clean |
| Serif | Sans-serif | Classic, professional |
| Display/Decorative | Sans-serif | Creative, bold |
| Monospace | Monospace | Technical, developer |

### Contrast Checker
Ensure text is readable:
- Large text (18px+): 3:1 contrast ratio
- Normal text: 4.5:1 contrast ratio
- Use tools like WebAIM Contrast Checker

## Quick Theme Generator

Request a custom theme:
```
Generate a theme for [purpose]:
- Mood: [professional/playful/elegant/bold]
- Industry: [tech/finance/creative/healthcare]
- Primary color preference: [color or "any"]
- Light or dark mode: [preference]
```

Example output:
```css
/* Custom theme for [purpose] */
:root {
  --bg-primary: [generated];
  --bg-secondary: [generated];
  --text-primary: [generated];
  --text-secondary: [generated];
  --accent: [generated];
  --accent-secondary: [generated];
  --font-heading: [selected];
  --font-body: [selected];
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
