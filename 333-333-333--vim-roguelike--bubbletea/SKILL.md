---
name: bubbletea
description: > Use when this capability is needed.
metadata:
  author: 333-333-333
---

## When to Use

- Building interactive terminal applications in Go
- Creating CLI tools with rich user interfaces
- Implementing forms, lists, tables in terminal
- Adding spinners, progress bars, or animations
- Designing multi-view terminal applications

## Core Concepts

### The Elm Architecture

Bubble Tea follows The Elm Architecture pattern:

```
┌─────────────────────────────────────────┐
│                                         │
│    ┌─────────┐      ┌─────────┐        │
│    │  Model  │─────▶│  View   │────────┼──▶ Terminal Output
│    └─────────┘      └─────────┘        │
│         ▲                              │
│         │                              │
│    ┌─────────┐      ┌─────────┐        │
│    │ Update  │◀─────│   Msg   │◀───────┼─── User Input / Events
│    └─────────┘      └─────────┘        │
│                                         │
└─────────────────────────────────────────┘
```

| Component | Purpose |
|-----------|---------|
| **Model** | Application state (struct) |
| **Msg** | Events that trigger state changes |
| **Update** | Handles messages, returns new model |
| **View** | Renders model to string for display |

## Basic Structure

```go
package main

import (
    "fmt"
    "os"
    
    tea "github.com/charmbracelet/bubbletea"
)

// Model holds application state
type model struct {
    cursor   int
    choices  []string
    selected map[int]struct{}
}

// Init returns initial command (can be nil)
func (m model) Init() tea.Cmd {
    return nil
}

// Update handles messages and returns updated model
func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tea.KeyMsg:
        switch msg.String() {
        case "ctrl+c", "q":
            return m, tea.Quit
        case "up", "k":
            if m.cursor > 0 {
                m.cursor--
            }
        case "down", "j":
            if m.cursor < len(m.choices)-1 {
                m.cursor++
            }
        case "enter", " ":
            if _, ok := m.selected[m.cursor]; ok {
                delete(m.selected, m.cursor)
            } else {
                m.selected[m.cursor] = struct{}{}
            }
        }
    }
    return m, nil
}

// View renders the UI as a string
func (m model) View() string {
    s := "Select items:\n\n"
    
    for i, choice := range m.choices {
        cursor := " "
        if m.cursor == i {
            cursor = ">"
        }
        
        checked := " "
        if _, ok := m.selected[i]; ok {
            checked = "x"
        }
        
        s += fmt.Sprintf("%s [%s] %s\n", cursor, checked, choice)
    }
    
    s += "\nPress q to quit.\n"
    return s
}

func main() {
    initialModel := model{
        choices:  []string{"Option 1", "Option 2", "Option 3"},
        selected: make(map[int]struct{}),
    }
    
    p := tea.NewProgram(initialModel)
    if _, err := p.Run(); err != nil {
        fmt.Printf("Error: %v", err)
        os.Exit(1)
    }
}
```

## Commands and Messages

### Custom Messages

```go
// Define custom message types
type tickMsg time.Time
type responseMsg struct {
    data string
    err  error
}

// Commands return messages
func tick() tea.Cmd {
    return tea.Tick(time.Second, func(t time.Time) tea.Msg {
        return tickMsg(t)
    })
}

func fetchData(url string) tea.Cmd {
    return func() tea.Msg {
        resp, err := http.Get(url)
        if err != nil {
            return responseMsg{err: err}
        }
        defer resp.Body.Close()
        
        body, _ := io.ReadAll(resp.Body)
        return responseMsg{data: string(body)}
    }
}

// Handle in Update
func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tickMsg:
        m.elapsed++
        return m, tick() // Continue ticking
        
    case responseMsg:
        if msg.err != nil {
            m.err = msg.err
            return m, nil
        }
        m.data = msg.data
        return m, nil
    }
    return m, nil
}
```

### Batch Commands

```go
// Run multiple commands at once
func (m model) Init() tea.Cmd {
    return tea.Batch(
        fetchData("https://api.example.com"),
        tick(),
        tea.EnterAltScreen,
    )
}
```

## Bubbles Components

### Text Input

```go
import "github.com/charmbracelet/bubbles/textinput"

type model struct {
    input textinput.Model
}

func initialModel() model {
    ti := textinput.New()
    ti.Placeholder = "Enter your name"
    ti.Focus()
    ti.CharLimit = 50
    ti.Width = 30
    
    return model{input: ti}
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    var cmd tea.Cmd
    m.input, cmd = m.input.Update(msg)
    return m, cmd
}

func (m model) View() string {
    return fmt.Sprintf("Name: %s\n\n%s", m.input.View(), "(esc to quit)")
}
```

### List Component

```go
import "github.com/charmbracelet/bubbles/list"

type item struct {
    title, desc string
}

func (i item) Title() string       { return i.title }
func (i item) Description() string { return i.desc }
func (i item) FilterValue() string { return i.title }

type model struct {
    list list.Model
}

func initialModel() model {
    items := []list.Item{
        item{title: "Item 1", desc: "Description 1"},
        item{title: "Item 2", desc: "Description 2"},
    }
    
    l := list.New(items, list.NewDefaultDelegate(), 30, 20)
    l.Title = "My List"
    
    return model{list: l}
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    var cmd tea.Cmd
    m.list, cmd = m.list.Update(msg)
    return m, cmd
}

func (m model) View() string {
    return m.list.View()
}
```

### Spinner

```go
import "github.com/charmbracelet/bubbles/spinner"

type model struct {
    spinner  spinner.Model
    loading  bool
}

func initialModel() model {
    s := spinner.New()
    s.Spinner = spinner.Dot
    s.Style = lipgloss.NewStyle().Foreground(lipgloss.Color("205"))
    
    return model{spinner: s, loading: true}
}

func (m model) Init() tea.Cmd {
    return m.spinner.Tick
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    if m.loading {
        var cmd tea.Cmd
        m.spinner, cmd = m.spinner.Update(msg)
        return m, cmd
    }
    return m, nil
}

func (m model) View() string {
    if m.loading {
        return fmt.Sprintf("%s Loading...", m.spinner.View())
    }
    return "Done!"
}
```

### Viewport (Scrollable Content)

```go
import "github.com/charmbracelet/bubbles/viewport"

type model struct {
    viewport viewport.Model
    ready    bool
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tea.WindowSizeMsg:
        if !m.ready {
            m.viewport = viewport.New(msg.Width, msg.Height-4)
            m.viewport.SetContent(longContent)
            m.ready = true
        } else {
            m.viewport.Width = msg.Width
            m.viewport.Height = msg.Height - 4
        }
    }
    
    var cmd tea.Cmd
    m.viewport, cmd = m.viewport.Update(msg)
    return m, cmd
}
```

## Styling with Lip Gloss

```go
import "github.com/charmbracelet/lipgloss"

var (
    // Colors
    subtle    = lipgloss.AdaptiveColor{Light: "#D9DCCF", Dark: "#383838"}
    highlight = lipgloss.AdaptiveColor{Light: "#874BFD", Dark: "#7D56F4"}
    
    // Styles
    titleStyle = lipgloss.NewStyle().
        Bold(true).
        Foreground(lipgloss.Color("#FAFAFA")).
        Background(highlight).
        Padding(0, 1)
    
    itemStyle = lipgloss.NewStyle().
        PaddingLeft(2)
    
    selectedStyle = lipgloss.NewStyle().
        PaddingLeft(2).
        Foreground(highlight).
        Bold(true)
    
    boxStyle = lipgloss.NewStyle().
        Border(lipgloss.RoundedBorder()).
        BorderForeground(highlight).
        Padding(1, 2)
)

func (m model) View() string {
    title := titleStyle.Render("My App")
    
    var items string
    for i, item := range m.items {
        if i == m.cursor {
            items += selectedStyle.Render("> " + item) + "\n"
        } else {
            items += itemStyle.Render("  " + item) + "\n"
        }
    }
    
    content := lipgloss.JoinVertical(lipgloss.Left, title, items)
    return boxStyle.Render(content)
}
```

## Multi-View Pattern

```go
type view int

const (
    listView view = iota
    detailView
    editView
)

type model struct {
    currentView view
    listModel   listModel
    detailModel detailModel
    editModel   editModel
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tea.KeyMsg:
        if msg.String() == "esc" && m.currentView != listView {
            m.currentView = listView
            return m, nil
        }
    }
    
    var cmd tea.Cmd
    switch m.currentView {
    case listView:
        m.listModel, cmd = m.listModel.Update(msg)
    case detailView:
        m.detailModel, cmd = m.detailModel.Update(msg)
    case editView:
        m.editModel, cmd = m.editModel.Update(msg)
    }
    return m, cmd
}

func (m model) View() string {
    switch m.currentView {
    case detailView:
        return m.detailModel.View()
    case editView:
        return m.editModel.View()
    default:
        return m.listModel.View()
    }
}
```

## Key Handling Patterns

```go
func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tea.KeyMsg:
        switch {
        // Exact key match
        case msg.String() == "q":
            return m, tea.Quit
            
        // Key type
        case msg.Type == tea.KeyEnter:
            // handle enter
            
        // Ctrl combinations
        case msg.Type == tea.KeyCtrlC:
            return m, tea.Quit
            
        // Multiple keys for same action
        case msg.String() == "up" || msg.String() == "k":
            m.cursor--
            
        // Alt combinations
        case msg.Alt && msg.String() == "enter":
            // alt+enter
        }
        
    case tea.WindowSizeMsg:
        m.width = msg.Width
        m.height = msg.Height
    }
    
    return m, nil
}
```

## Testing Bubble Tea

```go
func TestModel_Update(t *testing.T) {
    m := initialModel()
    
    // Simulate key press
    newModel, _ := m.Update(tea.KeyMsg{Type: tea.KeyDown})
    
    model := newModel.(model)
    assert.Equal(t, 1, model.cursor)
}

func TestModel_View(t *testing.T) {
    m := model{
        choices: []string{"A", "B"},
        cursor:  0,
    }
    
    view := m.View()
    
    assert.Contains(t, view, "> A")
    assert.Contains(t, view, "  B")
}
```

## Commands

```bash
# Install Bubble Tea
go get github.com/charmbracelet/bubbletea

# Install Bubbles (components)
go get github.com/charmbracelet/bubbles

# Install Lip Gloss (styling)
go get github.com/charmbracelet/lipgloss

# Run with alt screen (recommended for full TUI)
# In code: tea.NewProgram(model, tea.WithAltScreen())
```

## Ecosystem

| Package | Purpose |
|---------|---------|
| `bubbletea` | Core framework |
| `bubbles` | Pre-built components |
| `lipgloss` | Styling and layout |
| `harmonica` | Smooth animations |
| `glamour` | Markdown rendering |
| `glow` | Markdown viewer |
| `huh` | Form/survey library |

## Resources

- **Docs**: [Bubble Tea](https://github.com/charmbracelet/bubbletea)
- **Components**: [Bubbles](https://github.com/charmbracelet/bubbles)
- **Styling**: [Lip Gloss](https://github.com/charmbracelet/lipgloss)
- **Examples**: [Bubble Tea Examples](https://github.com/charmbracelet/bubbletea/tree/master/examples)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/333-333-333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
