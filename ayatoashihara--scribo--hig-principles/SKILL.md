---
name: hig-principles
description: Human Interface Guidelines (HIG) design principles from Apple, Google Material, and Microsoft Fluent. Use when designing user interactions, feedback systems, navigation patterns, or evaluating UI usability. Ensures interfaces feel natural, responsive, and learnable. Use when this capability is needed.
metadata:
  author: ayatoashihara
---

# Human Interface Guidelines (HIG) Principles

Universal principles for interfaces that feel natural and intuitive.

## Scope

**Use for:** Interaction design, feedback design, navigation patterns, usability evaluation, accessibility, responsive behavior, state management.

**Not for:** Visual styling specifics (use `/interface-design`), information architecture (use `/ooui-design`).

**Complements:** Use with `/ooui-design` for complete UI design.

---

# The Core Problem

Users don't read manuals. They bring expectations from every other app they've used.

When your interface violates these expectations:
- Users feel lost ("Where am I?")
- Users feel anxious ("Did that work?")
- Users feel frustrated ("Why won't this do what I want?")

**HIG principles prevent these feelings by aligning with how humans naturally think and act.**

---

# The Seven Principles

## 1. Mental Model Alignment

**Users have internal models of how things work. Match them.**

### Real-World Metaphors

Use familiar concepts from physical world or other software.

| Concept | Metaphor | User Expectation |
|---------|----------|------------------|
| Saving | 💾 Disk icon | "My work is preserved" |
| Deleting | 🗑️ Trash | "I can recover this" |
| Navigation | ← Back arrow | "Return to where I was" |
| Settings | ⚙️ Gear | "I can customize this" |
| Folders | 📁 Container | "Things are organized inside" |

### Consistency Patterns

Same action = same result, everywhere.

```
✅ Consistent
[Save] always saves
Ctrl+S always saves
"Save" means the same thing in every context

❌ Inconsistent  
[Save] sometimes saves, sometimes "saves draft"
Ctrl+S saves in one place, does nothing elsewhere
"Save" vs "Store" vs "Keep" vs "Preserve"
```

### Predictability Rules

1. **Buttons do what they say** — "Delete" deletes, "Cancel" cancels
2. **Icons match meaning** — ✏️ edits, 🗑️ deletes, ➕ adds
3. **Position implies function** — Top-right = close, bottom-right = confirm
4. **Color implies meaning** — Red = danger, green = success, blue = link

---

## 2. Direct Manipulation

**Users should feel they're touching the actual object, not issuing commands.**

### Principles

| Indirect (Command) | Direct (Manipulation) |
|-------------------|----------------------|
| Menu → Edit → Rename | Click on name → type new name |
| File → Move → Select folder | Drag file to folder |
| Edit → Delete | Select → press Delete key |
| Settings → Change color | Click color → pick new color |

### Implementation

```
Drag & Drop:
- Drag files to move/organize
- Drag items to reorder lists
- Drag to resize panels

Inline Editing:
- Click text to edit in place
- Double-click to enter edit mode
- Click away to confirm

Selection + Action:
- Select first, then act
- Multi-select with Shift/Ctrl
- Actions appear for selection
```

### Visual Feedback During Manipulation

```
Dragging:
- Ghost image follows cursor
- Drop zones highlight
- Invalid zones show ⛔

Resizing:
- Cursor changes to ↔️ or ↕️
- Live preview of new size
- Guides snap to common sizes

Selecting:
- Immediate visual change (highlight, checkbox)
- Count of selected items shown
- Bulk actions become available
```

---

## 3. Feedback & Response

**Every action must have an immediate, visible response.**

### The 0.4 Second Rule (Doherty Threshold)

Users lose engagement after 0.4 seconds of no response.

| Response Time | User Perception | Required Feedback |
|---------------|-----------------|-------------------|
| < 100ms | Instant | None needed |
| 100-400ms | Slight delay | Subtle indicator (cursor change) |
| 400ms-1s | Noticeable wait | Spinner or progress hint |
| 1-10s | Long wait | Progress bar + what's happening |
| > 10s | Very long | Progress + time estimate + cancel option |

### Feedback Types

**1. Visual Feedback**
```
Button click → Button depresses/changes color
Hover → Element highlights
Error → Red border + message
Success → Green checkmark + toast
Loading → Spinner or skeleton
```

**2. State Feedback**
```
Saving... → "Saving..." indicator
Saved → ✓ "Saved" (then fade)
Error → ⚠️ "Failed to save. [Retry]"
Offline → 📴 "You're offline. Changes saved locally."
```

**3. Progress Feedback**
```
Determinate (known duration):
████████░░ 80% - "Processing 80 of 100 items"

Indeterminate (unknown duration):
◌ "Analyzing document..."
```

### Labor Illusion

For AI/complex operations, show the work being done:

```
❌ Generic
"Processing..."

✅ Labor Illusion
"Analyzing structure..."
"Evaluating content..."
"Generating feedback..."
"Finalizing results..."
```

Users value results more when they see effort.

---

## 4. User Control & Freedom

**Users must always feel in control. Never trap them.**

### Escape Hatches

Every state must have an exit:

```
Modal → [✕] Close button + Escape key + Click outside
Wizard → [Back] + [Cancel] at every step
Form → [Cancel] discards changes
Process → [Cancel] stops operation
```

### Undo Everything

Destructive actions must be reversible:

```
[Delete Item]
    ↓
Toast: "Item deleted. [Undo]" (5 seconds)
    ↓
Click Undo → Item restored

NOT:
[Delete Item]
    ↓
Confirm: "Are you sure?" [Yes] [No]  ← Annoying, still destructive
```

**Undo > Confirmation dialogs**

### User-Initiated Actions Only

```
✅ Good
- Auto-save drafts silently
- User clicks [Publish] to go live

❌ Bad
- Auto-publish without consent
- Forced actions ("You must complete this")
- No way to opt out
```

### Customization

Let users adjust to their preferences:

```
- Keyboard shortcuts (customizable)
- Display density (compact/comfortable/spacious)
- Theme (light/dark/system)
- Default views (list/grid/table)
```

---

## 5. Affordance & Signifiers

**Elements must look like what they do.**

### Affordance = Capability

What an object CAN do:
- Button affords pressing
- Slider affords sliding
- Text field affords typing

### Signifier = Indicator

What signals the affordance:
- Raised appearance signals "pressable"
- Handle (⋮⋮) signals "draggable"
- Blinking cursor signals "type here"

### Common Signifiers

| Element | Signifier | Meaning |
|---------|-----------|---------|
| Button | Raised, colored | "Click me" |
| Link | Underline, blue | "Click to navigate" |
| Input | Border, placeholder | "Type here" |
| Draggable | Grip handle ⋮⋮ | "Drag to move" |
| Resizable | Corner/edge handles | "Drag to resize" |
| Expandable | Chevron ▶ | "Click to expand" |
| Dropdown | Caret ▼ | "Click for options" |
| Disabled | Grayed out | "Not available" |

### Interactive States

Every interactive element needs visible states:

```
Default   → Base appearance
Hover     → Slight highlight (cursor: pointer)
Active    → Pressed/depressed appearance
Focus     → Visible outline (accessibility)
Disabled  → Grayed, no pointer
Loading   → Spinner replaces content
```

---

## 6. Recognition Over Recall

**Show options, don't require memorization.**

### Recognition

```
✅ Show available actions
Toolbar: [Bold] [Italic] [Underline] [Link]
Context menu: [Cut] [Copy] [Paste] [Delete]
Dropdown: [Option 1] [Option 2] [Option 3]
```

### Recall

```
❌ Require user to remember
"Type command to execute"
"Enter the correct code"
"Remember to click X before Y"
```

### Implementation

| Recall (Bad) | Recognition (Good) |
|--------------|-------------------|
| Command line only | GUI + command line |
| Memorize shortcuts | Show shortcuts in menus |
| "Enter product ID" | Searchable product picker |
| Remember settings location | Contextual settings links |

### Search & Autocomplete

Help users find without memorizing:

```
Search: [pro...]
         ↓
Suggestions:
  📦 Products
  👤 Profiles  
  📊 Progress Reports
  ⚙️ Proxy Settings
```

---

## 7. Error Prevention & Recovery

**Prevent errors first. Handle them gracefully second.**

### Prevention Hierarchy

```
1. Make errors impossible
   - Disable invalid options
   - Constrain inputs (date picker vs text field)
   - Gray out unavailable actions

2. Make errors difficult
   - Confirmation for destructive actions
   - Validate before submission
   - Smart defaults

3. Make errors recoverable
   - Clear error messages
   - Undo functionality
   - Auto-save drafts
```

### Error Message Anatomy

```
❌ Bad Error Messages
"Error 500"
"Invalid input"
"Something went wrong"

✅ Good Error Messages
┌─────────────────────────────────────┐
│ ⚠️ Email address is invalid         │  ← What's wrong
│                                     │
│ Please enter a valid email like     │  ← How to fix
│ name@example.com                    │
│                                     │
│ [Try Again]                         │  ← Action to take
└─────────────────────────────────────┘
```

### Inline Validation

Validate as user types, not on submit:

```
Email: [john@]
       ⚠️ Please complete the email address

Email: [john@example.com]  
       ✓ Looks good
```

### Graceful Degradation

When things fail, fail gracefully:

```
Image failed to load:
┌─────────────────┐
│   🖼️            │
│ Image not found │
│ [Retry]         │
└─────────────────┘

NOT:
┌─────────────────┐
│      ✕          │  (broken image icon, no explanation)
└─────────────────┘
```

---

# Platform-Specific Guidelines

## Apple HIG Highlights

- **Deference**: Content is primary, UI recedes
- **Clarity**: Text is legible, icons are precise
- **Depth**: Layers create hierarchy and context
- **Haptics**: Physical feedback for actions (mobile)

## Material Design Highlights

- **Material as metaphor**: Surfaces have physical properties
- **Bold, intentional**: Typography and color create focus
- **Motion provides meaning**: Animation guides attention
- **Adaptive**: Responsive to device and context

## Fluent Design Highlights

- **Light**: Illumination guides focus
- **Depth**: Z-axis creates hierarchy
- **Motion**: Natural, physics-based animation
- **Material**: Translucency and texture add context
- **Scale**: Works from small to large screens

---

# Quick Checklist

## Before Shipping UI

### Mental Model
- [ ] Does it match user expectations?
- [ ] Are metaphors familiar?
- [ ] Is vocabulary consistent?

### Direct Manipulation
- [ ] Can users touch/drag objects?
- [ ] Is editing inline where possible?
- [ ] Does selection precede action?

### Feedback
- [ ] Does every click respond immediately?
- [ ] Are loading states clear?
- [ ] Do errors explain what went wrong?

### User Control
- [ ] Can users escape any state?
- [ ] Is undo available for destructive actions?
- [ ] Are preferences customizable?

### Affordance
- [ ] Do interactive elements look interactive?
- [ ] Are all states visually distinct?
- [ ] Is disabled state obvious?

### Recognition
- [ ] Are options visible, not hidden?
- [ ] Can users search/filter to find things?
- [ ] Are shortcuts shown, not just available?

### Errors
- [ ] Are invalid states prevented?
- [ ] Do error messages explain the fix?
- [ ] Is recovery always possible?

---

# Commands

- `/hig:audit` — Audit UI against HIG principles
- `/hig:feedback` — Design feedback system for an action
- `/hig:states` — Define all states for a component
- `/hig:errors` — Design error handling for a flow

---

# Quick Reference

```
7 HIG Principles:

1. MENTAL MODEL    → Match user expectations
2. DIRECT MANIP    → Touch objects, not commands
3. FEEDBACK        → Respond to every action (< 0.4s)
4. USER CONTROL    → Always provide escape + undo
5. AFFORDANCE      → Look like what you do
6. RECOGNITION     → Show options, don't require memory
7. ERROR HANDLING  → Prevent > Detect > Recover

Response Times:
< 100ms  → Instant, no feedback needed
< 400ms  → Subtle indicator
< 1s     → Spinner
< 10s    → Progress bar + label
> 10s    → Progress + estimate + cancel

Error Messages Must Include:
1. What went wrong (specific)
2. Why it happened (if helpful)
3. How to fix it (actionable)
4. Action button (clear next step)

Interactive States:
Default → Hover → Active → Focus → Disabled → Loading
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayatoashihara) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
