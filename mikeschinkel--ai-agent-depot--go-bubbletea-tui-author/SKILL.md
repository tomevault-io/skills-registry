---
name: go-bubbletea-tui-author
description: hard-won lessons from building Bubble Tea UIs. Read this BEFORE implementing new models or components to avoid repeating mistakes. Use when authoring new BubbleTea models and/or any development of TUI apps using Bubble Tea and/or Bubble Tea component elements. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# Bubble Tea TUI (Text User Interface) Author

This document captures hard-won lessons from building Bubble Tea UIs. Read this BEFORE implementing new models or components to avoid repeating mistakes.

## 🔥 THE GOLDEN RULE 🔥

**TRUST LIPGLOSS. DON'T SECOND-GUESS IT.**

If you find yourself with "empirical adjustment" constants to fix widths, **you have a bug in your understanding**, not in lipgloss. Stop, step back, and find the root cause.

## Quick Reference

**Most Common Mistakes:**
1. ❌ Making fixes without measuring actual values → Add debug logging first!
2. ❌ Thinking borders are 2 chars per side → They're 1 char per side (2 total)
3. ❌ Not understanding Width() semantics → Width = totalWidth - borderWidth
4. ❌ Manually subtracting padding/border from widths → Trust lipgloss to handle it
5. ❌ Lying to child components about their size via `SetSize()` → Use natural widths
6. ❌ Modifying content width to handle odd columns → Use dynamic padding instead
7. ❌ Thinking child components handle borders → Parent model applies borders
8. ❌ Using empirical adjustments as band-aids → Find and fix the root cause

**Key Facts:**
- **DEBUG FIRST!** Measure calculated vs actual widths before making assumptions
- `Width()` in lipgloss includes padding but excludes border
- Borders are 1 character per side (2 total), not 2 per side (4 total)
- To get total width of 37: use `Width(37 - 2)` = `Width(35)`
- Parent models apply borders, child components don't know about them
- Auto-sized panes (no `Width()` set) determine their own size
- Constrained panes (with `Width()` set) use allocated space
- Dynamic properties (like padding) are better than modifying content width

---

## Width Calculations: Trust Lipgloss

### THE CRITICAL RULE

**🚨 TRUST LIPGLOSS - DON'T SECOND-GUESS IT 🚨**

You will be tempted to manually calculate widths by subtracting padding and border. **DON'T.** Lipgloss handles spacing internally. Your job is to:
1. Allocate space
2. Let lipgloss fit content into that space

### HOW LIPGLOSS WIDTH() WORKS

**Key fact:** `Width()` in lipgloss **includes padding but excludes border**.

```go
style := lipgloss.NewStyle().
    Width(100).              // Total pane width (includes padding, excludes border)
    PaddingLeft(1).          // 1 char inside the width
    BorderStyle(RoundedBorder())  // 4 chars OUTSIDE the width (2 per side)
```

Visual breakdown:
```
Border(1) | Pad(1) | Content | Pad(1) | Border(1)
|<------- Width(100) ------->|
|<------- Total Visual Width = 102 -------->|
```

**⚠️ CRITICAL: Width() includes padding but excludes border!**

If you want total rendered width of 37:
```go
const borderWidth = 2 // 1 left + 1 right
totalWidth := 37
widthForLipgloss := totalWidth - borderWidth  // 35

style := lipgloss.NewStyle().
    Width(widthForLipgloss).  // 35 (includes padding)
    PaddingLeft(1).
    PaddingRight(2).
    BorderStyle(lipgloss.RoundedBorder())

// Result: 35 + 2 (border) = 37 total rendered width ✓
```

### TWO APPROACHES TO SIZING

**Approach 1: Auto-sizing (no Width() set)**
```go
// Lipgloss auto-sizes to content
style := lipgloss.NewStyle().
    PaddingLeft(1).
    PaddingRight(1).
    BorderStyle(lipgloss.RoundedBorder())

// Visual width = content + padding + border (all auto-calculated)
```
Use for: Dynamic content like trees where you don't know the width in advance.

**Approach 2: Constrained sizing (Width() set)**
```go
// You specify the width, lipgloss fits content inside
style := lipgloss.NewStyle().
    Width(totalWidth).  // You control this
    PaddingLeft(1).
    BorderStyle(lipgloss.RoundedBorder())

// Lipgloss ensures final visual width = totalWidth + border
```
Use for: Fixed-width panes like code viewers.

### THE TWO-WIDTH PROBLEM

**You still need TWO width values, but for a different reason than you think:**

1. **Total Width** - For lipgloss `Width()` (what the pane occupies)
2. **Content Width** - For child components `SetSize()` (what they can render)

**Track both in your model:**

```go
type MyModel struct {
    paneContentWidth int  // For viewport.SetSize()
    paneTotalWidth   int  // For lipgloss Width()
}
```

**But DON'T try to derive one from the other by subtracting constants!**

### THE CORRECT CALCULATION PATTERN

```go
func (m MyModel) calculateLayout() MyModel {
    // Calculate total width based on available space
    halfTotal := remainingWidth / 2
    m.paneTotalWidth = halfTotal  // This is what we allocate

    // For content width: TRUST LIPGLOSS
    // Just use the same base value - lipgloss handles the rest
    m.paneContentWidth = halfTotal  // Same value!

    return m
}
```

**Why are they the same?** Because lipgloss handles padding and border internally when you call `Width()`. You don't need to account for it manually.

### WHO HANDLES BORDERS?

**Critical understanding:** The **parent model** applies borders, not child components.

```go
// Parent model (e.g., CommitReviewModel)
func (m MyModel) View() string {
    childContent := m.childModel.View()  // Child returns raw content

    // Parent wraps with border
    style := lipgloss.NewStyle().BorderStyle(lipgloss.RoundedBorder())
    return style.Render(childContent)
}
```

This means:
- Child models (bubbletree, viewport, etc.) have NO knowledge of borders
- Parent calculates total width including border
- Child receives content width via `SetSize()`
- Parent applies border when rendering

---

## Lipgloss Border Widths

**⚠️ CRITICAL: Border width is 1 character per side, not 2!**

**Single borders (RoundedBorder, NormalBorder, etc.):**
- 1 character per side (the box-drawing character: │, ╭, ╮, ╰, ╯)
- 2 characters total width (left + right)
- Use `const borderWidth = 2` for single borders

**Double borders (DoubleBorder):**
- Still 1 character per side (just different Unicode characters)
- 2 characters total width
- Use `const borderWidth = 2`

**Common mistake:** Thinking borders are 2 chars per side because they "look thick". They're single Unicode characters!

---

## Padding Calculations

**Common mistake:** Forgetting to account for ALL padding.

```go
// Tree pane with padding on both sides
treeStyle := lipgloss.NewStyle().
    PaddingLeft(1).   // 1 char
    PaddingRight(1)   // 1 char
// Total padding width = 2

// Code pane with padding on one side only
codeStyle := lipgloss.NewStyle().
    PaddingLeft(1)    // 1 char
// Total padding width = 1
```

**Track padding separately per pane type:**
```go
const (
    treePaddingWidth = 2  // Left + Right
    codePaddingWidth = 1  // Left only
)
```

---

## Immutable Struct Pattern

### THE PATTERN

**Methods should update struct fields and return the model:**

```go
// ✅ CORRECT - Bubble Tea pattern
func (m MyModel) calculateLayout() MyModel {
    m.contentWidth = /* calculate */
    m.totalWidth = /* calculate */
    return m
}

// ❌ WRONG - Leads to verbose code and errors
func (m MyModel) calculateLayout() (contentWidth, totalWidth int) {
    return /* calculate */, /* calculate */
}
```

### WHY

1. **Follows Bubble Tea conventions** - All model updates return updated model
2. **Caches values** - Calculated once, used multiple times
3. **Reduces errors** - No manual assignment of multiple return values
4. **Cleaner call sites:**

```go
// ✅ CORRECT
m = m.calculateLayout()
// Values available as m.contentWidth, m.totalWidth

// ❌ WRONG
contentWidth, totalWidth := m.calculateLayout()
// Now you have to track these variables separately
```

---

## Equal Width Panes: The Odd Width Problem

**Problem:** When splitting remaining width between two panes, odd numbers create unequal panes.

**❌ WRONG SOLUTION:** Modify content width artificially

```go
// DON'T DO THIS!
if remainingWidth % 2 == 1 {
    treeContentWidth++   // Lying to the child model about its size
    treeTotalWidth++
    remainingWidth--
}
```

**Why this is wrong:**
- You're telling the child model it has more width than it naturally needs
- The child doesn't use the extra space
- Creates confusion between calculated and actual widths
- You're fighting lipgloss instead of working with it

**✅ CORRECT SOLUTION:** Use dynamic padding to consume odd columns

```go
// In your model struct
type MyModel struct {
    treeRightPadding int  // 1 or 2, depending on odd/even
}

// In calculateLayout()
func (m MyModel) calculateLayout() MyModel {
    m.treeRightPadding = 1  // Default

    // Calculate what remaining width WOULD be
    normalTreeTotal := treeContentWidth + 1 + 1 + 4  // left pad + right pad + border
    potentialRemaining := terminalWidth - normalTreeTotal

    // If both panes visible and remaining would be odd, add extra padding
    if bothPanesVisible && potentialRemaining % 2 == 1 {
        m.treeRightPadding = 2  // Extra column as padding
    }

    // Now calculate actual tree total with dynamic padding
    m.treeTotalWidth = treeContentWidth + 1 + m.treeRightPadding + 4

    remainingWidth = terminalWidth - m.treeTotalWidth
    // remainingWidth is now guaranteed even!

    return m
}

// In View()
func (m MyModel) View() string {
    treeStyle := lipgloss.NewStyle().
        PaddingLeft(1).
        PaddingRight(m.treeRightPadding).  // Dynamic!
        BorderStyle(lipgloss.RoundedBorder())

    return treeStyle.Render(m.treePane.View())
}
```

**Why this is correct:**
- Tree content width stays natural (from `LayoutWidth()`)
- Extra column becomes whitespace inside the border
- Lipgloss handles it all - you're working WITH it
- No lies to child components about their size
- Code panes guaranteed equal

---

## Empirical Verification vs. Empirical Adjustments

### EMPIRICAL VERIFICATION (Good!)

**When debugging width issues, verify empirically:**

1. **Use test pattern in file:**
```
   01234567890123456789012345678901234567890...
   ```

2. **Take screenshot showing exact cutoff point**

3. **Count characters to measure actual vs expected**

4. **Trust the compiler over gopls** - `go build` is source of truth

**This helps you find the ROOT CAUSE.**

### EMPIRICAL ADJUSTMENTS (Code Smell!)

**❌ RED FLAG: Constants like this:**

```go
const (
    paneTotalAdjustment   = 3  // "Empirically determined"
    paneContentAdjustment = 1  // "Empirically determined"
)

// Used like:
m.paneTotalWidth = halfTotal + paneTotalAdjustment
m.paneContentWidth = halfTotal + paneContentAdjustment
```

**Why this is a problem:**
- You're compensating for a calculation error, not fixing it
- Adjustments are magic numbers - no one knows WHY they're needed
- Brittle - breaks when layout changes
- You're fighting lipgloss instead of working with it

**What to do instead:**
1. **Find the root cause** - Why do you need the adjustment?
2. **Common root causes:**
- Not trusting lipgloss (manually subtracting padding/border)
- Lying to child components about their size
- Not accounting for dynamic properties (like padding)
- Mixing up Width() vs content width
3. **Fix the calculation** - Eliminate the need for adjustments
4. **If you truly need adjustments** - Document WHY with code comments explaining the reason

**The goal:** All width adjustments should be 0. If they're not, you have a bug in your understanding or implementation.

---

## Calculation Order and Best Practices

**Correct order when mixing auto-sized and constrained panes:**

1. **Calculate auto-sized pane width first** (e.g., tree from `LayoutWidth()`)
2. **Determine dynamic properties** (e.g., padding based on odd/even)
3. **Calculate auto-sized pane total** (content + dynamic padding + border)
4. **Calculate remaining width** for constrained panes
5. **Distribute remaining width** (guaranteed even if step 2 handled it)

```go
// 1. Auto-sized content width
treeContentWidth = tree.LayoutWidth()

// 2. Dynamic padding to handle odd widths
treeRightPadding = 1  // Default
normalTreeTotal := treeContentWidth + 1 + 1 + 4
potentialRemaining := terminalWidth - normalTreeTotal
if potentialRemaining % 2 == 1 {
    treeRightPadding = 2  // Consume odd column
}

// 3. Auto-sized total with dynamic padding
treeTotalWidth = treeContentWidth + 1 + treeRightPadding + 4

// 4. Remaining for constrained panes
remainingWidth = terminalWidth - treeTotalWidth

// 5. Distribute (now guaranteed even)
halfWidth := remainingWidth / 2
pane2TotalWidth = halfWidth
pane3TotalWidth = halfWidth
pane2ContentWidth = halfWidth  // Trust lipgloss
pane3ContentWidth = halfWidth  // Trust lipgloss
```

**Key principles:**
- Auto-sized panes (no `Width()` set) determine their own size
- Constrained panes (with `Width()` set) use allocated space
- Handle odd columns with dynamic padding, NOT by lying about content width
- Trust lipgloss to handle padding and border - don't manually subtract

---

## When to Recalculate Layout

**Recalculate on:**
- `tea.WindowSizeMsg` - Terminal resized
- Toggling pane visibility
- Changing content that affects natural width (like tree)

**Store results in model fields:**
```go
case tea.WindowSizeMsg:
    m.terminalWidth = msg.Width
    m.terminalHeight = msg.Height
    m = m.recalculateLayout()  // Recalc and update viewports
```

---

## Overlays: Compositing Foreground on Background

### THE PROBLEM

You need to overlay one Bubble Tea component on top of another (modal dialogs, dropdowns, tooltips). Standard string concatenation doesn't work because:

1. **ANSI escape codes** - Syntax highlighting and lipgloss styling embed ANSI codes
2. **String width vs byte length** - `len(str)` counts ANSI codes, breaking positioning
3. **Positioning must be visual** - Overlays need to appear at specific screen columns

### THE SOLUTION

**Use ANSI-aware string operations from `github.com/charmbracelet/x/ansi`:**

```go
import "github.com/charmbracelet/x/ansi"

// ✅ CORRECT - Visual width
width := ansi.StringWidth(styledText)

// ❌ WRONG - Byte length (includes ANSI codes)
width := len(styledText)
```

### OVERLAY PATTERN

**Proven pattern from bubbledd and bubblemodal:**

```go
// OverlayDropdown overlays foreground view on background view at specified position.
// Uses ANSI-aware string operations to correctly handle styled text.
//
// Parameters:
//   - background: The base view (fully rendered string with ANSI codes)
//   - foreground: The overlay view (fully rendered string with ANSI codes)
//   - row: Line number in background where foreground row 0 should appear (0-indexed)
//   - col: Display column in background where foreground col 0 should appear (0-indexed)
//
// Returns:
//   - Composited view with foreground overlaid on background
func OverlayDropdown(background, foreground string, row, col int) string {
    var result strings.Builder

    bgLines := strings.Split(background, "\n")
    fgLines := strings.Split(foreground, "\n")

    for i, bgLine := range bgLines {
        fgRow := i - row

        // This line has no foreground overlay
        if fgRow < 0 || fgRow >= len(fgLines) {
            result.WriteString(bgLine)
            result.WriteString("\n")
            continue
        }

        // Overlay foreground line onto background line
        fgLine := fgLines[fgRow]
        composited := overlayLine(bgLine, fgLine, col)
        result.WriteString(composited)
        result.WriteString("\n")
    }

    // Remove trailing newline
    output := result.String()
    if len(output) > 0 && output[len(output)-1] == '\n' {
        output = output[:len(output)-1]
    }

    return output
}
```

**Per-line overlay (the critical piece):**

```go
// overlayLine overlays foreground onto background at column position (ANSI-aware).
// Pattern: left part of background + foreground + right part of background
//
// The key insight: Standard Go string operations (len, slicing) count ANSI escape
// codes as characters, which breaks positioning. We use ansi.StringWidth() for
// visual width and ansi.Truncate/TruncateLeft for ANSI-safe string cutting.
func overlayLine(background, foreground string, col int) string {
    if col < 0 {
        col = 0
    }

    bgWidth := ansi.StringWidth(background)
    fgWidth := ansi.StringWidth(foreground)

    var result strings.Builder

    // Left part: truncate background to col width
    if col > 0 {
        if col <= bgWidth {
            left := ansi.Truncate(background, col, "")
            result.WriteString(left)
        } else {
            // Need padding beyond background width
            result.WriteString(background)
            result.WriteString(strings.Repeat(" ", col-bgWidth))
        }
    }

    // Middle part: foreground content (the overlay)
    result.WriteString(foreground)

    // Right part: remainder of background after foreground
    endCol := col + fgWidth
    if endCol < bgWidth {
        // TruncateLeft(s, n) skips the first n display columns
        remaining := ansi.TruncateLeft(background, endCol, "")
        result.WriteString(remaining)
    }

    return result.String()
}
```

### ANSI-AWARE STRING OPERATIONS

**From `github.com/charmbracelet/x/ansi`:**

| Operation | Description |
|-----------|-------------|
| `ansi.StringWidth(s)` | Visual width (what you see on screen) |
| `ansi.Truncate(s, width, tail)` | Keep first N visual columns (cut from right) |
| `ansi.TruncateLeft(s, width, tail)` | Skip first N visual columns (cut from left) |

**Example:**

```go
styled := lipgloss.NewStyle().Foreground(lipgloss.Color("205")).Render("Hello")
// styled contains ANSI escape codes

len(styled)                    // ❌ 18 (includes escape codes)
ansi.StringWidth(styled)       // ✅ 5  (visual width)

// Keep first 3 visual characters
truncated := ansi.Truncate(styled, 3, "")  // "Hel" (still styled!)

// Skip first 2 visual characters
remaining := ansi.TruncateLeft(styled, 2, "")  // "llo" (still styled!)
```

### USAGE IN YOUR MODEL

**In your View() method:**

```go
func (m MyModel) View() string {
    // Render main content
    mainView := m.mainContent.View()

    // If modal/dropdown is active, overlay it
    if m.showModal {
        modalView := m.modal.View()

        // Calculate position (typically centered)
        row := (m.height - m.modal.Height()) / 2
        col := (m.width - m.modal.Width()) / 2

        // Overlay modal on main view
        return OverlayModal(mainView, modalView, row, col)
    }

    return mainView
}
```

### POSITIONING HELPERS

**Center overlay on screen:**

```go
row := (screenHeight - overlayHeight) / 2
col := (screenWidth - overlayWidth) / 2
```

**Position below trigger element:**

```go
row := triggerRow + 1
col := triggerCol
```

**Right-align overlay:**

```go
row := /* desired row */
col := screenWidth - overlayWidth
```

### REFERENCE IMPLEMENTATIONS

- **gommod/bubbledd/overlay_dropdown.go** - Dropdown pattern
- **gommod/bubblemodal/overlay_modal.go** - Modal pattern

Both use identical overlay logic. The difference is in usage context, not implementation.

---

## Debugging Width Issues: The Systematic Approach

**🔥 CRITICAL LESSON: Debug first, fix second. Never make "confident" claims without measuring actual values! 🔥**

### THE PROBLEM PATTERN

You calculate widths mathematically, it looks perfect on paper, but the UI has a gap. You try fixes based on assumptions. Nothing works. User gets frustrated.

**Why:** You're debugging your mental model, not the actual code.

### THE SOLUTION: MEASURE EVERYTHING

**Step 1: Add debug logging IMMEDIATELY**

Don't guess. Don't assume. Measure actual runtime values:

```go
// In calculateLayout()
m.Logger.Info("WIDTH DEBUG calculateLayout",
    "terminalWidth", m.terminalWidth,
    "treeContentWidth", m.treeContentWidth,
    "treeTotalWidth", m.treeTotalWidth,
    "splitTotalWidth", m.splitTotalWidth,
    "splitContentWidth", m.splitContentWidth,
)

// In View() - measure what actually renders
treeRendered := treeStyle.Render(m.treePane.View())
treeActualWidth := 0
if lines := strings.Split(treeRendered, "\n"); len(lines) > 0 {
    treeActualWidth = ansi.StringWidth(lines[0])
}

m.Logger.Info("WIDTH DEBUG View",
    "treeCalculated", m.treeTotalWidth,
    "treeActual", treeActualWidth,
    "gap", m.treeTotalWidth - treeActualWidth,
)
```

**Step 2: Run the app, capture logs**

```bash
cd gommod && ../cmd/gomion/gomion commit 2>&1 | tee /tmp/debug.log
# Then: grep "WIDTH DEBUG" /tmp/debug.log
```

**Step 3: Compare calculated vs actual**

Look for discrepancies:
- `treeCalculated: 37` but `treeActual: 34` → Tree is 3 chars too narrow!
- `splitCalculated: 283` and `splitActual: 283` → Split pane is correct!

**Step 4: Find the root cause**

In the example above:
- Tree is 3 chars short
- 3 = border width (2) + 1
- Hypothesis: Width() might include padding but exclude border
- Test: Change `Width(contentWidth)` to `Width(totalWidth - borderWidth)`
- Verify: Check logs again

### COMMON DISCREPANCIES AND CAUSES

| Symptom | Likely Cause |
|---------|--------------|
| Actual = Calculated - 2 | Forgot to account for borders (1 per side) |
| Actual = Calculated - 3 | Width() excludes border, you didn't subtract it |
| Actual < Calculated | Content is narrower than you think, not being padded |
| Actual > Calculated | You're adding padding/border twice |

### MEASURE CHILD CONTENT TOO

Don't just measure the final rendered output. Measure intermediate values:

```go
// Measure tree content BEFORE styling
treeContent := m.treePane.View()
treeContentActualWidth := 0
if lines := strings.Split(treeContent, "\n"); len(lines) > 0 {
    treeContentActualWidth = ansi.StringWidth(lines[0])
}

m.Logger.Info("WIDTH DEBUG tree details",
    "treeContentWidth", m.treeContentWidth,      // What you calculated
    "treeContentActualWidth", treeContentActualWidth,  // What it really is
    "treeTotalWidth", m.treeTotalWidth,          // What you expect total
    "treeRenderedWidth", treeActualWidth,        // What actually rendered
)
```

This reveals:
- Is the child producing the width you expect?
- Is lipgloss styling it correctly?
- Where exactly is the discrepancy?

### THE GOLDEN RULE

**Never make a "fix" without first understanding the discrepancy.**

❌ "I'll add 3 to the width, that should fix it"
❌ "Let me try subtracting the border here"
❌ "Maybe if I adjust the padding..."

✅ "Logs show actual is 34 but calculated is 37. That's a 3-char gap. Let me investigate why."
✅ "Tree content is 17 chars but I'm setting Width(32). Is lipgloss padding it correctly?"
✅ "Width() might include padding. Let me verify with documentation."

### AFTER THE FIX

Leave the debug logging in place! Comment it out if needed, but don't delete it. Next time layout breaks, you'll be glad you can uncomment and immediately see the values.

```go
// DEBUG: Uncomment to debug width calculations
// m.Logger.Info("WIDTH DEBUG calculateLayout", ...)
```

---

## Common Mistakes Checklist

Before declaring "width calculations are done":

- [ ] **Did you add debug logging?** Measure calculated vs actual widths!
- [ ] **Did you verify with logs?** Run the app and check "WIDTH DEBUG" output
- [ ] **Are calculated and actual widths equal?** If not, find root cause first!
- [ ] **Are you trusting lipgloss?** No manual subtraction of padding/border?
- [ ] **Do you understand Width() semantics?** Width = totalWidth - borderWidth
- [ ] **Are border widths correct?** 1 char per side (2 total), not 2 per side!
- [ ] **Do you have BOTH content and total width fields** for each pane?
- [ ] **Does `calculateLayout()` return updated model** (not multiple ints)?
- [ ] **Are all empirical adjustments zero?** If not, find the root cause!
- [ ] **Are you using dynamic properties** (like padding) to handle odd widths?
- [ ] **Are you lying to child components?** Don't tell them wrong sizes via `SetSize()`
- [ ] **Do you understand who applies borders?** (Parent model, not child)
- [ ] **Did you verify empirically** with test pattern?
- [ ] **Are auto-sized panes truly auto-sizing?** (No `Width()` set)
- [ ] **Are constrained panes properly constrained?** (`Width()` set correctly)

Before implementing overlay compositing:

- [ ] Are you using `ansi.StringWidth()` instead of `len()`?
- [ ] Are you using `ansi.Truncate()` / `ansi.TruncateLeft()` for string cutting?
- [ ] Are both background and foreground fully rendered (with all styling)?
- [ ] Did you handle the case where overlay extends beyond background?
- [ ] Did you test with styled/colored text to ensure ANSI codes don't break?

---

## Reference Implementations

### Width Calculations
See `gommod/gomtui/commit_review_model.go` for complete reference:
- Width cache fields in struct (lines ~47-52)
- `calculateLayout()` populates cache and returns model (lines ~480-530)
- `View()` uses total widths directly (lines ~221-270)
- `recalculateLayout()` uses content widths for viewports (lines ~558-573)

### Overlay Compositing
See:
- `gommod/bubbledd/overlay_dropdown.go` - Complete overlay implementation
- `gommod/bubblemodal/overlay_modal.go` - Same pattern, different context

---

**When in doubt, refer to this document. When you discover a new pattern, ADD IT HERE.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
