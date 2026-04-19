---
name: tauri-native-menus
description: > Use when this capability is needed.
metadata:
  author: stefkors
---

# Tauri v2 Native Context Menus

Guide for implementing native context menus in Tauri v2 applications. This skill documents the correct approach and common pitfalls when integrating native menus with a React frontend.

## When to Apply

Reference this guide when:

- Adding right-click context menus to UI elements
- Implementing popup menus in Tauri v2
- Migrating from web-based menus to native menus
- Debugging menu event handling issues

## Critical Knowledge

### Do NOT Use muda Directly

Tauri v2 uses [muda](https://github.com/tauri-apps/muda) internally for menu management, but **do not use muda's event system directly** in a Tauri application.

**Why it fails:**

- `muda::MenuEvent::set_event_handler()` does not receive events in Tauri's event loop
- `muda::MenuEvent::receiver()` channel never receives menu click events
- Tauri manages its own event loop and intercepts muda events

**What happens:**

```rust
// This will NOT work - events never fire
muda::MenuEvent::set_event_handler(Some(|event| {
    // This closure is never called in Tauri context
}));

// This also does NOT work
let receiver = muda::MenuEvent::receiver();
if let Ok(event) = receiver.try_recv() {
    // Never receives events
}
```

### Use Tauri's Native Menu API

Instead, use Tauri's built-in menu API which properly integrates with its event system:

```rust
use tauri::menu::{MenuBuilder, MenuItem, PredefinedMenuItem};
use tauri::Window;

#[tauri::command]
pub async fn show_context_menu(
    window: Window,
    request: ShowContextMenuRequest,
) -> Result<(), String> {
    // Build menu using Tauri's API
    let mut menu_builder = MenuBuilder::new(&window);

    for item in &request.items {
        match item {
            ContextMenuItemOrSeparator::Item(menu_item) => {
                // Use compound ID format: "ctx:{request_id}:{item_id}"
                let compound_id = format!("ctx:{}:{}", request.request_id, menu_item.id);
                let tauri_item = MenuItem::with_id(
                    &window,
                    &compound_id,
                    &menu_item.label,
                    !menu_item.disabled.unwrap_or(false),
                    None::<&str>,
                ).map_err(|e| e.to_string())?;
                menu_builder = menu_builder.item(&tauri_item);
            }
            ContextMenuItemOrSeparator::Separator => {
                let separator = PredefinedMenuItem::separator(&window)
                    .map_err(|e| e.to_string())?;
                menu_builder = menu_builder.item(&separator);
            }
        }
    }

    let menu = menu_builder.build().map_err(|e| e.to_string())?;

    // Show as popup menu at cursor position
    window.popup_menu(&menu).map_err(|e| e.to_string())?;

    Ok(())
}
```

### Handle Events in App Setup

Register a global menu event handler in your `lib.rs` setup:

```rust
// In tauri::Builder::default().setup(|app| { ... })
app.on_menu_event(move |app, event| {
    let event_id = event.id().0.as_str();

    // Handle context menu events (format: "ctx:{request_id}:{item_id}")
    if event_id.starts_with("ctx:") {
        let parts: Vec<&str> = event_id.splitn(3, ':').collect();
        if parts.len() == 3 {
            let request_id = parts[1];
            let item_id = parts[2];
            app.emit(
                "context-menu:clicked",
                serde_json::json!({
                    "requestId": request_id,
                    "itemId": item_id,
                }),
            ).ok();
        }
        return;
    }

    // Handle other menu events...
});
```

### Frontend Event Handling

Listen for context menu events in React:

```tsx
import { listen } from "@tauri-apps/api/event"

// Store a stable requestId per component instance
const requestIdRef = useRef(`file-row-${file.path}-${Date.now()}`)
const handlersRef = useRef<Map<string, () => void>>(new Map())

// Set up handlers map
useEffect(() => {
  const handlers = new Map<string, () => void>()
  handlers.set("action-1", handleAction1)
  handlers.set("action-2", handleAction2)
  handlersRef.current = handlers
}, [handleAction1, handleAction2])

// Listen for context menu events
useEffect(() => {
  const requestId = requestIdRef.current
  const unlisten = listen<{ requestId: string; itemId: string }>(
    "context-menu:clicked",
    (event) => {
      if (event.payload.requestId === requestId) {
        const handler = handlersRef.current.get(event.payload.itemId)
        if (handler) handler()
      }
    },
  )
  return () => {
    unlisten.then((fn) => fn())
  }
}, [])

// Trigger context menu on right-click
const handleContextMenu = useCallback(async (e: React.MouseEvent) => {
  e.preventDefault()
  e.stopPropagation()

  await showContextMenu({
    requestId: requestIdRef.current,
    items: [
      { type: "item", id: "action-1", label: "Action 1" },
      { type: "separator" },
      { type: "item", id: "action-2", label: "Action 2" },
    ],
  })
}, [])
```

## Menu Positioning

### Use Cursor Position (Recommended)

Let the OS handle menu positioning by not specifying coordinates:

```rust
// Pass None for position - menu appears at cursor
window.popup_menu(&menu)?;
```

### Avoid Manual Coordinate Conversion

Do NOT try to convert frontend coordinates to screen coordinates manually:

```rust
// DON'T DO THIS - coordinate systems differ between:
// - CSS pixels (logical) vs physical pixels
// - Window-relative vs screen-relative
// - Different scale factors on Retina displays
let screen_x = window_pos.x + client_x; // WRONG
```

## TypeScript Types

```typescript
export interface ContextMenuItem {
  id: string
  label: string
  disabled?: boolean
}

export type ContextMenuItemOrSeparator =
  | { type: "item"; id: string; label: string; disabled?: boolean }
  | { type: "separator" }

export interface ShowContextMenuRequest {
  requestId: string
  items: ContextMenuItemOrSeparator[]
  x?: number // Optional, not used when relying on cursor position
  y?: number
}
```

## Rust Types

```rust
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ContextMenuItem {
    pub id: String,
    pub label: String,
    pub disabled: Option<bool>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(tag = "type")]
pub enum ContextMenuItemOrSeparator {
    #[serde(rename = "item")]
    Item(ContextMenuItem),
    #[serde(rename = "separator")]
    Separator,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]  // Important: match frontend naming
pub struct ShowContextMenuRequest {
    pub request_id: String,
    pub items: Vec<ContextMenuItemOrSeparator>,
    pub x: Option<f64>,
    pub y: Option<f64>,
}
```

## Common Mistakes

| Mistake                                      | Why It Fails                                      | Solution                  |
| -------------------------------------------- | ------------------------------------------------- | ------------------------- |
| Using `muda::MenuEvent::set_event_handler()` | Tauri intercepts muda events                      | Use `app.on_menu_event()` |
| Using `muda::MenuEvent::receiver()`          | Events never delivered to channel                 | Use `app.on_menu_event()` |
| Manual coordinate conversion                 | CSS vs physical pixels, scale factors             | Let OS position at cursor |
| Missing `#[serde(rename_all = "camelCase")]` | Frontend sends camelCase, Rust expects snake_case | Add serde attribute       |
| Using `&AppHandle` in `MenuItem::with_id()`  | Borrow doesn't implement `Manager`                | Use `&window` instead     |

## Dependencies

```toml
# Cargo.toml - muda is NOT needed as a direct dependency
# Tauri v2 includes it internally

[dependencies]
tauri = { version = "2", features = ["menu"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

## References

- [Tauri v2 Menu Documentation](https://v2.tauri.app/learn/menu/)
- [muda GitHub](https://github.com/tauri-apps/muda) (for understanding internals)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stefkors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
