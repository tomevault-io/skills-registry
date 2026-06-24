---
name: windows-3-1-web-designer
description: Modern web applications with authentic Windows 3.1 aesthetic. Solid navy title bars, Program Manager navigation, beveled borders, single window controls. Extrapolates Win31 to AI chatbots (Cue Use when this capability is needed.
metadata:
  author: curiositech
---

# Windows 3.1 Web Designer

Creates modern 2026 web applications with authentic Windows 3.1 aesthetic. Not recreating 1992—**extrapolating Win31 to modern contexts**: AI assistants as Cue Card systems, mobile as pocket organizers, responsive as tiled MDI windows.

## When to Use

**Use for:**
- Web apps with Win31 authenticity (documentation sites, retro dashboards)
- AI chatbot interfaces (Cue Card-style assistants, wizard dialogs)
- Mobile-responsive Win31 UIs (pocket computing paradigm)
- Program Manager navigation patterns
- Tiled/cascading window layouts
- MDI (Multiple Document Interface) applications
- Hotdog Stand mode easter eggs

**Do NOT use for:**
- Windows 95 aesthetic → use **windows-95-web-designer** (gradients, Start menu, taskbar)
- Vaporwave/synthwave → use **vaporwave-glassomorphic-ui-designer** (neons, gradients)
- macOS/iOS styling → use **native-app-designer**
- Flat/Material design → use **web-design-expert**

## Win31 vs Win95: Critical Differences

| Feature | Windows 3.1 | Windows 95 |
|---------|-------------|------------|
| Title bar | **Solid navy** (#000080) | Gradient (dark→light blue) |
| Window controls | **Single menu button** | Three buttons (−, □, ×) |
| Navigation | **Program Manager** | Start Menu + Taskbar |
| Fonts | **Bitmap/VT323** | MS Sans Serif, Tahoma |
| Icons | **32×32 flat** | 32×32 with drop shadow |
| Depth | **Bevels only** | Bevels + subtle gradients |
| AI style | **Cue Cards, Wizards** | Clippy character |

---

## Core Design System

### Color Palette

| Color | Hex | CSS Variable | Usage |
|-------|-----|--------------|-------|
| System Gray | #c0c0c0 | `--win31-gray` | THE primary background |
| Dark Gray | #808080 | `--win31-dark-gray` | Shadows, pressed states |
| Light Gray | #dfdfdf | `--win31-light-gray` | Highlights |
| Navy | #000080 | `--win31-navy` | Title bar (SOLID, no gradient) |
| Teal | #008080 | `--win31-teal` | Desktop, links, highlights |
| White | #ffffff | `--win31-white` | Beveled highlights, inputs |
| Black | #000000 | `--win31-black` | Beveled shadows, borders |

### The Win31 Title Bar (SOLID)

**THE signature Win31 element** - solid navy, no gradient:

```css
.win31-titlebar {
  background: #000080; /* SOLID - never a gradient! */
  color: white;
  font-family: 'VT323', 'Courier New', monospace;
  font-weight: bold;
  font-size: 11px;
  padding: 3px 6px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.win31-titlebar-inactive {
  background: #808080; /* Solid dark gray when inactive */
}
```

### Window Control Button (Single)

Win31 has ONE control button (not Win95's three):

```css
.win31-control-btn {
  width: 18px;
  height: 14px;
  background: var(--win31-gray);
  border: none;
  font-size: 8px;
  font-family: var(--font-pixel);

  /* 3D bevel - outset */
  box-shadow:
    inset -1px -1px 0 var(--win31-black),
    inset 1px 1px 0 var(--win31-white),
    inset -2px -2px 0 var(--win31-dark-gray),
    inset 2px 2px 0 var(--win31-light-gray);
}

.win31-control-btn:active {
  box-shadow:
    inset 1px 1px 0 var(--win31-black),
    inset -1px -1px 0 var(--win31-white);
}
```

### The Sacred Rule: Beveled Borders

**OUTSET (Raised)** - Buttons, toolbars:
- Top/Left border: WHITE
- Bottom/Right border: BLACK
- Inner: light-gray TL, dark-gray BR

**INSET (Sunken)** - Text fields, content areas:
- Top/Left border: DARK GRAY
- Bottom/Right border: WHITE
- Inner: black TL, light-gray BR

### Typography

| Use | Font | Fallback | Size |
|-----|------|----------|------|
| UI Labels | VT323 | Courier New | 12px |
| Title bars | VT323 Bold | Courier New Bold | 11px |
| Headings | Press Start 2P | VT323 | 14px |
| Code | IBM Plex Mono | Courier Prime | 12px |

**Web font stack:**
```css
:root {
  --font-win31-ui: 'VT323', 'Courier New', monospace;
  --font-win31-pixel: 'Press Start 2P', 'VT323', monospace;
  --font-win31-code: 'IBM Plex Mono', 'Courier Prime', monospace;
}
```

---

## Modern Extrapolations

### AI Chatbots: The Cue Card Paradigm

Win31 would present AI as a **modal wizard system**, not an animated character:

```
┌─ AI Assistant ──────────────────────[─]─┐
│                                          │
│  ┌────────────────────────────────────┐  │
│  │  What would you like help with?    │  │
│  │                                    │  │
│  │  ○ Writing a document              │  │
│  │  ○ Working with files              │  │
│  │  ○ Setting up your system          │  │
│  │  ○ Learning Windows basics         │  │
│  └────────────────────────────────────┘  │
│                                          │
│         [  OK  ]  [ Cancel ]  [ Help ]   │
└──────────────────────────────────────────┘
```

**Key patterns:**
- No animated characters (pre-Clippy)
- One question at a time (modal)
- Radio button/numbered choices
- Step-by-step wizard indicators
- Cue Cards floating alongside app

### Mobile: The Pocket Computing Paradigm

Win31 on mobile extrapolates to **pocket organizer with Program Manager**:

```
┌────────────────────────────┐
│ ■ Program Manager ─ 10:45  │  ← Title bar with time
├────────────────────────────┤
│ ╔════════════════════════╗ │
│ ║ Main                  ║ │
│ ╠════════════════════════╣ │
│ ║  ┌───┐   ┌───┐        ║ │
│ ║  │📁│   │📝│        ║ │  ← Program group
│ ║  │Mgr│   │Wrt│        ║ │
│ ║  └───┘   └───┘        ║ │
│ ╚════════════════════════╝ │
├────────────────────────────┤
│ [ Window ]  [ Help ]       │  ← Menu bar bottom
└────────────────────────────┘
```

**Key patterns:**
- Program Manager is navigation (not Start menu)
- One window at a time (modal stack)
- Dialog stack pattern (overlays cascade)
- Menu bar at bottom for touch
- Swipe left = close window

### Responsive: MDI as Breakpoints

Win31 used Multiple Document Interface. Apply this:

| Breakpoint | Win31 Metaphor | Layout |
|------------|----------------|--------|
| Mobile (&lt;640px) | Pocket computing | Single window, modal dialogs |
| Tablet (640-1024px) | Laptop | Cascading windows |
| Desktop (&gt;1024px) | Full desktop | Tiled MDI windows |

### Theming: Hotdog Stand and Beyond

Windows 3.1 had limited but memorable themes:

```css
/* Hotdog Stand (the infamous red/yellow) */
[data-theme="hotdog-stand"] {
  --win31-gray: #ff0000;
  --win31-dark-gray: #800000;
  --win31-light-gray: #ff8080;
  --win31-navy: #ffff00;
  --win31-title-text: #ff0000;
}

/* Monochrome (high contrast) */
[data-theme="monochrome"] {
  --win31-gray: #ffffff;
  --win31-dark-gray: #000000;
  --win31-light-gray: #ffffff;
  --win31-navy: #000000;
  --win31-teal: #000000;
}
```

---

## Component Patterns

### Program Manager Window

```css
.win31-program-manager {
  position: fixed;
  inset: 0;
  background: var(--win31-teal);
  display: flex;
  flex-direction: column;
}

.win31-program-group {
  background: var(--win31-gray);
  border: 3px solid var(--win31-black);
  box-shadow:
    inset 2px 2px 0 var(--win31-white),
    inset -2px -2px 0 var(--win31-dark-gray);
  margin: 8px;
  min-width: 200px;
}

.win31-program-group-titlebar {
  background: var(--win31-navy);
  color: var(--win31-white);
  padding: 2px 6px;
  font-family: var(--font-pixel);
  font-size: 10px;
  font-weight: bold;
}

.win31-program-icons {
  display: grid;
  grid-template-columns: repeat(auto-fill, 64px);
  gap: 8px;
  padding: 12px;
}
```

### Dialog Box

```css
.win31-dialog {
  min-width: 300px;
  background: var(--win31-gray);
  border: 3px solid var(--win31-black);
  box-shadow:
    inset 2px 2px 0 var(--win31-white),
    inset -2px -2px 0 var(--win31-dark-gray),
    4px 4px 0 var(--win31-black);
}

.win31-dialog-content {
  padding: 16px;
  display: flex;
  gap: 16px;
}

.win31-dialog-icon {
  width: 32px;
  height: 32px;
  flex-shrink: 0;
}

.win31-dialog-buttons {
  display: flex;
  justify-content: center;
  gap: 8px;
  padding: 8px 16px 16px;
}
```

### Menu Bar

```css
.win31-menubar {
  background: var(--win31-gray);
  border-bottom: 2px solid var(--win31-dark-gray);
  padding: 2px;
  display: flex;
  gap: 0;
}

.win31-menu-item {
  padding: 4px 12px;
  font-family: var(--font-pixel);
  font-size: 11px;
  cursor: pointer;
}

.win31-menu-item:hover,
.win31-menu-item--active {
  background: var(--win31-navy);
  color: var(--win31-white);
}

.win31-menu-dropdown {
  position: absolute;
  background: var(--win31-gray);
  border: 2px solid;
  border-color: var(--win31-white) var(--win31-black)
               var(--win31-black) var(--win31-white);
  min-width: 150px;
  z-index: 100;
}
```

### Status Bar

```css
.win31-statusbar {
  display: flex;
  gap: 2px;
  padding: 2px;
  background: var(--win31-gray);
  border-top: 2px solid var(--win31-dark-gray);
}

.win31-statusbar-panel {
  flex: 1;
  padding: 2px 8px;
  font-family: var(--font-pixel);
  font-size: 10px;
  border: 1px solid;
  border-color: var(--win31-dark-gray) var(--win31-white)
               var(--win31-white) var(--win31-dark-gray);
}
```

---

## Anti-Patterns

### Anti-Pattern: Title Bar Gradients

**Novice thinking**: "Win31 has blue title bars like Win95"
**Reality**: Win31 has SOLID navy. Gradient is Win95 only.
**Instead**: `background: #000080` (never `linear-gradient`)

### Anti-Pattern: Three Window Buttons

**Novice thinking**: "Windows has minimize, maximize, close"
**Reality**: Win31 has a SINGLE menu button (−). The three-button pattern is Win95.
**Instead**: One button that opens a system menu

### Anti-Pattern: Start Menu/Taskbar

**Novice thinking**: "Windows navigation = Start button"
**Reality**: Win31 uses Program Manager. No taskbar, no Start.
**Instead**: Program groups with cascading/tiled windows

### Anti-Pattern: Neon Colors

**Novice thinking**: #00d4ff, #ff00ff for retro feel
**Reality**: This is vaporwave, not Win31
**Instead**: Muted palette: teal (#008080), navy (#000080), gray (#c0c0c0)

### Anti-Pattern: Rounded Corners

**Novice thinking**: `border-radius: 8px` for friendly UI
**Reality**: Win31 has sharp 90° corners everywhere
**Instead**: No border-radius (or 0)

### Anti-Pattern: Blur Effects

**Novice thinking**: `backdrop-filter: blur(10px)`, soft shadows
**Reality**: Win31 has no blur—only hard-edge bevels
**Instead**: Sharp bevel shadows: `box-shadow: 4px 4px 0 #000`

### Anti-Pattern: Dark Backgrounds

**Novice thinking**: Dark mode = #1a1a2e backgrounds
**Reality**: Win31 is LIGHT. System gray (#c0c0c0) everywhere.
**Instead**: Light gray base with teal desktop

### Anti-Pattern: Clippy-Style Characters

**Novice thinking**: Win31 had assistant characters
**Reality**: Clippy came with Office 97 (Win95 era). Win31 used Cue Cards.
**Instead**: Modal dialogs, step-by-step wizards, floating help cards

---

## Quick Decision Tree

```
Is it a window chrome element?
├── Title bar? → SOLID navy (no gradient!)
├── Control button? → SINGLE button (not three)
├── Button? → 3D bevel (white TL, black BR)
├── Input? → Inset bevel (dark TL, white BR)
└── Content area? → White or gray, flat

Is it navigation?
├── Primary nav? → Program Manager groups
├── Section nav? → Menu bar with dropdowns
├── Page nav? → List box or tree control
└── Actions? → Toolbar buttons (beveled)

Is it AI/help?
├── Onboarding? → Setup Wizard (Step X of Y)
├── Inline help? → Cue Cards (floating tips)
├── Questions? → Modal dialog with options
└── Feedback? → Message box with icon

Is it responsive?
├── Mobile? → Single window, modal stack
├── Tablet? → Cascading windows
└── Desktop? → Tiled MDI layout
```

---

## CSS Variables Template

```css
:root {
  /* Core palette */
  --win31-white: #ffffff;
  --win31-black: #000000;
  --win31-gray: #c0c0c0;
  --win31-dark-gray: #808080;
  --win31-light-gray: #dfdfdf;
  --win31-navy: #000080;
  --win31-teal: #008080;

  /* Semantic */
  --win31-error: #ff0000;
  --win31-warning: #ffff00;
  --win31-success: #00ff00;
  --win31-info: #0000ff;

  /* Typography */
  --font-win31-ui: 'VT323', 'Courier New', monospace;
  --font-win31-pixel: 'Press Start 2P', 'VT323', monospace;
  --font-win31-code: 'IBM Plex Mono', 'Courier Prime', monospace;

  /* Spacing (4px grid) */
  --win31-spacing-xs: 2px;
  --win31-spacing-sm: 4px;
  --win31-spacing-md: 8px;
  --win31-spacing-lg: 16px;
  --win31-spacing-xl: 24px;
}
```

---

## The Quick Test

If your component has:
- ❌ Any gradient title bars → NOT Win31 (that's Win95)
- ❌ Three window buttons → NOT Win31 (that's Win95)
- ❌ Start menu or taskbar → NOT Win31 (that's Win95)
- ❌ Any neon colors → NOT Win31 (that's vaporwave)
- ❌ Any rounded corners → NOT Win31
- ❌ Any blur effects → NOT Win31
- ❌ Animated assistant character → NOT Win31 (that's Clippy/Win95)

It should have:
- ✅ Solid navy (#000080) title bar
- ✅ Single window control button
- ✅ Program Manager navigation
- ✅ System gray (#c0c0c0) base
- ✅ Beveled borders (white TL, black BR)
- ✅ Sharp corners everywhere
- ✅ Pixel fonts (VT323, Press Start 2P)
- ✅ Hard-edge box shadows only
- ✅ Cue Cards for help (not characters)

---

## File Naming Conventions

For authentic Win31 feel:
- All caps: `README.TXT`, `INSTALL.EXE`
- 8.3 format: `PROGRAM.EXE`, `CONFIG.SYS`
- Extensions matter: `.WRI`, `.BMP`, `.INI`

---

## References

- `/references/design-system.md` - Complete color palette, typography, beveled border patterns
- `/references/component-patterns.md` - Full CSS for windows, buttons, forms, panels
- `/references/anti-patterns.md` - Vaporwave comparison, decision tree, conversion examples
- `/references/ai-assistant-patterns.md` - Cue Card-style AI UX patterns
- `/references/mobile-pocket-computing.md` - Responsive Win31 for mobile

---

## Pairs With

- **windows-95-web-designer** - For gradient/taskbar Win95 aesthetic
- **vaporwave-glassomorphic-ui-designer** - Different retro aesthetic (neons)
- **web-design-expert** - For brand direction alongside retro style
- **design-system-creator** - For generating full design token systems

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/curiositech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
