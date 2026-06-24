---
name: tui-testing-debug
description: Methods for debugging and unit testing Bubble Tea TUIs without a visible terminal. Use when this capability is needed.
metadata:
  author: anishshah1803
---

## What I do
- Implement logging to file (standard TUI debugging).
- Setup `teatest` for automated TUI testing.
- Handle terminal recovery logic.

## Best Practices
1. **Log to File:** Since `fmt.Println` breaks the TUI, use `tea.LogToFile("debug.log", "prefix")` for development.
2. **Testing with Teatest:** Use `github.com/charmbracelet/bubbletea/teatest` to simulate keypresses and assert the final view string.
3. **Panic Recovery:** Use `tea.WithAltScreen()` and ensure errors are returned through `p.Run()` rather than calling `os.Exit` inside the model.

## Example: File Logging
```go
if len(os.Getenv("DEBUG")) > 0 {
    f, err := tea.LogToFile("debug.log", "debug")
    if err != nil {
        log.Fatal(err)
    }
    defer f.Close()
}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anishshah1803) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
