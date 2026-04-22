---
name: terminal-ui-design
description: Create distinctive, production-grade terminal user interfaces with high design quality. Use this skill when the user asks to build CLI tools, TUI applications, or terminal-based interfaces. Generates creative, polished code that avoids generic terminal aesthetics. Use when this capability is needed.
metadata:
  author: t0dorakis
---

# Terminal UI Design Skill

Create distinctive, production-grade terminal user interfaces with high design quality. Generate creative, polished code that avoids generic terminal aesthetics.

## Design Thinking

Before coding, understand the context and commit to a **BOLD** aesthetic direction:

1. **Purpose**: What problem does this interface solve? Who uses it? What's the workflow?
2. **Tone**: Pick an extreme aesthetic from the palette below
3. **Constraints**: Technical requirements (Python Rich, Go bubbletea, Rust ratatui, Node.js blessed/ink, pure ANSI escape codes, ncurses)
4. **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember about this terminal experience?

Choose a clear conceptual direction and execute it with precision. A dense information dashboard and a zen single-focus interface both work—the key is **intentionality**, not intensity.

## Aesthetic Palette

Choose ONE and commit fully:

| Aesthetic                | Character                      | Colors                                                      | Typography                      |
| ------------------------ | ------------------------------ | ----------------------------------------------------------- | ------------------------------- |
| **Cyberpunk/Hacker**     | Glitchy, dangerous, alive      | Hot pink `#ff00ff`, electric cyan `#00ffff`, deep purple bg | Monospace with Unicode glitches |
| **Retro Computing**      | Nostalgic, warm, authentic     | Amber `#ffb000` or green `#00ff00` on black                 | Chunky ASCII art                |
| **Minimalist Zen**       | Quiet, focused, calming        | Muted grays, single accent color                            | Generous whitespace, sparse     |
| **Maximalist Dashboard** | Dense, powerful, professional  | Information-coded colors                                    | Tight grids, compact            |
| **Synthwave/Neon**       | 80s future, vibrant            | Magenta, cyan, purple gradients                             | Stylized headers                |
| **Monochrome Brutalist** | Bold, stark, uncompromising    | Single color, white on black                                | Heavy borders, blocks           |
| **Corporate Mainframe**  | Professional, trustworthy      | Blue-gray, minimal color                                    | Clean tables, structured        |
| **Playful/Whimsical**    | Fun, approachable, human       | Bright primaries, emojis                                    | Rounded corners, icons          |
| **Matrix-Style**         | Code rain, digital, mysterious | Green on black only                                         | Cascading characters            |
| **Military/Tactical**    | Urgent, precise, no-nonsense   | OD green, amber warnings                                    | Grid coordinates, timestamps    |
| **Art Deco**             | Elegant, geometric, luxurious  | Gold, black, cream                                          | Decorative frames               |
| **Vaporwave**            | Dreamy, surreal, glitchy       | Pink, blue, purple pastels                                  | Japanese characters, waves      |

## Box Drawing & Borders

Choose border styles that match your aesthetic:

```
Single line:    ┌─────────┐    Clean, modern
                │         │
                └─────────┘

Double line:    ╔═════════╗    Bold, formal, retro-mainframe
                ║         ║
                ╚═════════╝

Rounded:        ╭─────────╮    Soft, friendly, modern
                │         │
                ╰─────────╯

Heavy:          ┏━━━━━━━━━┓    Strong, industrial
                ┃         ┃
                ┗━━━━━━━━━┛

ASCII only:     +---------+    Retro, universal compatibility
                |         |
                +---------+

Block chars:    █▀▀▀▀▀▀▀▀█    Chunky, bold, brutalist
                █         █
                █▄▄▄▄▄▄▄▄█
```

**Advanced techniques**:

- Asymmetric borders (double top, single sides)
- Decorative corners: `◆ ◈ ✦ ⬡ ● ◢ ◣`
- Mixed styles for hierarchy (heavy for primary, light for secondary)

## Color & Theme Implementation

### ANSI 16 (Universal)

```
Black   Red     Green   Yellow  Blue    Magenta Cyan    White
\x1b[30m \x1b[31m \x1b[32m \x1b[33m \x1b[34m \x1b[35m \x1b[36m \x1b[37m
Bright: \x1b[90m through \x1b[97m
```

### True Color (24-bit)

```
Foreground: \x1b[38;2;R;G;Bm
Background: \x1b[48;2;R;G;Bm
```

### Signature Palettes

**Cyberpunk**:

```
Background: #1a0a2e (deep purple)
Primary:    #ff00ff (hot pink)
Secondary:  #00ffff (electric cyan)
Accent:     #ff6b6b (coral warning)
```

**Amber Terminal**:

```
Background: #000000
Primary:    #ffb000 (warm amber)
Dim:        #805800 (dark amber)
Bright:     #ffd966 (light amber)
```

**Nord-Inspired**:

```
Background: #2e3440 (polar night)
Primary:    #88c0d0 (frost blue)
Secondary:  #a3be8c (aurora green)
Accent:     #bf616a (aurora red)
```

### Gradient Fills

Use block characters for gradients:

```
░▒▓█ — Light to solid
▁▂▃▄▅▆▇█ — Height progression (for charts)
```

## Typography & Text Styling

### Text Decorations

```
Bold:          \x1b[1m
Dim:           \x1b[2m
Italic:        \x1b[3m
Underline:     \x1b[4m
Strikethrough: \x1b[9m
Reverse:       \x1b[7m
Reset:         \x1b[0m
```

### Header Styles

**Block ASCII** (figlet-style):

```
███████╗██╗██╗     ███████╗███████╗
██╔════╝██║██║     ██╔════╝██╔════╝
█████╗  ██║██║     █████╗  ███████╗
██╔══╝  ██║██║     ██╔══╝  ╚════██║
██║     ██║███████╗███████╗███████║
╚═╝     ╚═╝╚══════╝╚══════╝╚══════╝
```

**Letter Spacing**:

```
S T A T U S    R E P O R T
```

**Section Markers**:

```
▶ SECTION NAME
[ SECTION ]
─── SECTION ───────────────────
◆ SECTION ◆
═══ SECTION ═══════════════════
```

### Unicode Enhancement

Replace boring characters with styled alternatives:

| Instead of    | Use            |
| ------------- | -------------- |
| `-` bullet    | `▸ › ◉ ⬢ ★ ⚡` |
| `*` star      | `★ ⭐ ✦ ✧`     |
| `>` arrow     | `→ ➜ ⟶ ▶`      |
| `[x]` check   | `✓ ✔ ◉ ●`      |
| `[ ]` empty   | `○ ◯ ☐`        |
| `...` loading | `⋯ ⠿ ···`      |

## Layout & Spatial Composition

### Panel Layout Example

```
╭─── SYSTEM MONITOR ──────────────────────────────╮
│                                                  │
│  CPU  [████████████░░░░░░░░]  67%               │
│  MEM  [██████████████░░░░░░]  74%               │
│  DSK  [████░░░░░░░░░░░░░░░░]  23%               │
│                                                  │
├──────────────────────────────────────────────────┤
│  ▸ Process count: 847                            │
│  ▸ Uptime: 14d 7h 23m                            │
│  ▸ Load avg: 2.34 1.89 1.67                      │
│                                                  │
╰──────────────────────────────────────────────────╯
```

### Column Layout

```
┌─ SERVERS ────────┐  ┌─ ALERTS ─────────┐
│                  │  │                  │
│ ● web-prod-1     │  │ ⚠ High CPU       │
│ ● web-prod-2     │  │ ✓ All healthy    │
│ ○ web-staging    │  │                  │
│                  │  │                  │
└──────────────────┘  └──────────────────┘
```

### Hierarchy Principles

- **Primary**: Bold, high contrast, prominent position
- **Secondary**: Normal weight, slightly dimmed
- **Tertiary**: Dim text, small, peripheral
- **Chrome**: Borders, labels, decorations—should not compete with content

## Motion & Animation

### Spinners

```python
# Braille dots
frames = ['⠋', '⠙', '⠹', '⠸', '⠼', '⠴', '⠦', '⠧', '⠇', '⠏']

# Orbital
frames = ['◐', '◓', '◑', '◒']

# Line
frames = ['|', '/', '-', '\\']

# Dots
frames = ['⣾', '⣽', '⣻', '⢿', '⡿', '⣟', '⣯', '⣷']

# Moon phases
frames = ['🌑', '🌒', '🌓', '🌔', '🌕', '🌖', '🌗', '🌘']
```

### Progress Bars

```
Standard:   [████████████░░░░░░░░]  60%
Minimal:    ████████████▒▒▒▒▒▒▒▒  60%
Fancy:      ⟨▰▰▰▰▰▰▱▱▱▱⟩  60%
Blocks:     █████████████░░░░░░░░  60%
```

### Transitions

```python
# Typing effect
for char in text:
    print(char, end='', flush=True)
    time.sleep(0.03)

# Wipe reveal (character by character per line)
for i, line in enumerate(lines):
    print(f"\x1b[{i+1};1H{line}")
    time.sleep(0.05)
```

## Data Visualization

### Sparklines

```
Inline chart: ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁
Usage trend:  CPU ▂▃▅▇▆▃▂▁▂▄▆▇▅▃
```

### Status Indicators

```
● Online     ○ Offline    ◐ Partial
✓ Success    ✗ Failed     ⟳ Pending
▲ Critical   ▼ Low        ● Normal
```

### Tree Structure

```
├── src/
│   ├── main.py
│   ├── utils/
│   │   ├── helpers.py
│   │   └── config.py
│   └── tests/
│       └── test_main.py
└── README.md
```

### Tables

```
┌──────────────┬─────────┬──────────┐
│ SERVICE      │ STATUS  │ LATENCY  │
├──────────────┼─────────┼──────────┤
│ api-gateway  │ ● UP    │   12ms   │
│ auth-service │ ● UP    │    8ms   │
│ db-primary   │ ○ DOWN  │    --    │
└──────────────┴─────────┴──────────┘
```

## Library Quick Reference

### Python: Rich

```python
from rich.console import Console
from rich.panel import Panel
from rich.table import Table
from rich.progress import Progress
from rich.live import Live

console = Console()
console.print("[bold magenta]Hello[/] [cyan]World[/]")
console.print(Panel("Content", title="Title", border_style="green"))
```

### Python: Textual (TUI Framework)

```python
from textual.app import App
from textual.widgets import Header, Footer, Static

class MyApp(App):
    CSS = """
    Screen {
        background: #1a0a2e;
    }
    """
    def compose(self):
        yield Header()
        yield Static("Hello, World!")
        yield Footer()
```

### Go: Bubbletea + Lipgloss

```go
import (
    "github.com/charmbracelet/lipgloss"
    tea "github.com/charmbracelet/bubbletea"
)

var style = lipgloss.NewStyle().
    Bold(true).
    Foreground(lipgloss.Color("#FF00FF")).
    Background(lipgloss.Color("#1a0a2e")).
    Padding(1, 2)
```

### Rust: Ratatui

```rust
use ratatui::{
    prelude::*,
    widgets::{Block, Borders, Paragraph},
};

let block = Block::default()
    .title("Title")
    .borders(Borders::ALL)
    .border_style(Style::default().fg(Color::Cyan));
```

### Node.js: Ink (React for CLI)

```tsx
import { render, Box, Text } from "ink";

const App = () => (
  <Box flexDirection="column" padding={1}>
    <Text bold color="magenta">
      Hello
    </Text>
    <Text color="cyan">World</Text>
  </Box>
);

render(<App />);
```

### Pure ANSI Escape Codes

```python
# Colors
print("\x1b[38;2;255;0;255mHot Pink\x1b[0m")
print("\x1b[48;2;26;10;46m\x1b[38;2;0;255;255mCyan on Purple\x1b[0m")

# Cursor control
print("\x1b[2J")         # Clear screen
print("\x1b[H")          # Home position
print("\x1b[5;10H")      # Move to row 5, col 10
print("\x1b[?25l")       # Hide cursor
print("\x1b[?25h")       # Show cursor
```

## Anti-Patterns to Avoid

**NEVER** produce generic terminal output like:

```
❌ Plain unformatted text output
❌ Default colors without intentional palette
❌ Basic [INFO], [ERROR] prefixes without styling
❌ Simple "----" dividers
❌ Walls of unstructured text
❌ Generic progress bars without personality
❌ Boring help text formatting
❌ Inconsistent spacing and alignment
❌ Mixed border styles without purpose
❌ Color vomit (too many colors without hierarchy)
```

## Design Checklist

Before finalizing any terminal UI:

- [ ] **Aesthetic chosen**: One clear direction, executed fully
- [ ] **Color palette**: Cohesive, 3-5 colors max
- [ ] **Typography hierarchy**: Primary, secondary, tertiary distinction
- [ ] **Borders**: Consistent style matching the aesthetic
- [ ] **Spacing**: Intentional padding and margins
- [ ] **Status indicators**: Styled, not default text
- [ ] **Loading states**: Animated, themed spinners/progress
- [ ] **Error states**: Styled, not generic red text
- [ ] **Empty states**: Designed, not blank

## Example: Complete Themed Interface

**Cyberpunk System Monitor**:

```
╔══════════════════════════════════════════════════════════════╗
║  ▓▓▓ NEURAL•LINK ▓▓▓  ◢◤ SYSTEM DIAGNOSTIC v2.7 ◢◤         ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  ⟨ CORE METRICS ⟩                                            ║
║  ┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄   ║
║                                                              ║
║  CPU ████████████████████░░░░░░░░░░ 67% ▲ 2.4GHz             ║
║  MEM ██████████████████████████░░░░ 84% ◆ 13.4/16GB          ║
║  NET ▁▂▃▅▆▇▅▃▂▁▂▄▆▇▅▃▂▁▂▃▅ IN: 847 MB/s                      ║
║                                                              ║
║  ⟨ ACTIVE PROCESSES ⟩                            [47 total]  ║
║  ┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄   ║
║                                                              ║
║  PID     NAME              CPU    MEM    STATUS              ║
║  ─────────────────────────────────────────────────────       ║
║  1847    chrome            23%    1.2G   ● ACTIVE            ║
║  2394    node              12%    847M   ● ACTIVE            ║
║  0847    postgres           8%    2.1G   ● ACTIVE            ║
║  3721    backup_daemon      0%     47M   ○ IDLE              ║
║                                                              ║
╠══════════════════════════════════════════════════════════════╣
║  ◢ SYS OK ◣  │  ⚡ UPTIME: 14d 7h │  ▼ TEMP: 62°C  │  ⟳ 2.4s ║
╚══════════════════════════════════════════════════════════════╝
```

The terminal is a canvas with unique constraints and possibilities. Don't just print text—**craft an experience**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t0dorakis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
