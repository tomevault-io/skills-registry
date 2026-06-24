---
name: ui-testing
description: This skill should be used when the user asks to "write UI tests", "add XCUITest", "test accessibility", "automate macOS UI", or needs guidance on XCTest patterns, accessibility identifiers, keyboard shortcut testing, or menu item automation for macOS SwiftUI apps. Use when this capability is needed.
metadata:
  author: artimath
---

# UI Testing Skill for macOS SwiftUI Apps

## Overview
Comprehensive patterns for testing macOS SwiftUI apps using XCTest and XCUITest.

## Test Architecture

### Test Pyramid
- **Unit tests** (Swift Testing `@Test`): Model logic, algorithms, pure functions
- **Integration tests** (Swift Testing): Component interactions, data flow
- **UI tests** (XCTest `XCTestCase`): User interactions, menu items, keyboard shortcuts

### Framework Usage
```swift
// Unit/Integration - Use Swift Testing
import Testing

@Test func confidencePropagation() {
    // Fast, isolated tests
}

// UI Automation - Use XCTest (required for XCUIApplication)
import XCTest

class MyAppUITests: XCTestCase {
    var app: XCUIApplication!

    override func setUpWithError() throws {
        continueAfterFailure = false
        app = XCUIApplication()
        app.launch()
    }
}
```

## Accessibility Identifiers

**CRITICAL**: Every testable element needs an accessibility identifier.

```swift
// SwiftUI View
Canvas { ... }
    .accessibilityIdentifier("graphCanvas")

Button("Save") { ... }
    .accessibilityIdentifier("saveButton")

// Node in Canvas - use node ID
.accessibilityIdentifier("node-\(node.id)")
```

### Naming Convention
- Canvas: `"graphCanvas"`
- Nodes: `"node-{id}"`
- Edges: `"edge-{id}"`
- Inspectors: `"inspector-document"`, `"inspector-element"`, etc.
- Toolbar buttons: `"toolbar-{action}"`

## Testing Patterns

### 1. Menu Item Tests
```swift
func testNewEntityMenuItem() throws {
    let menuBar = app.menuBars
    menuBar.menuBarItems["Entity"].click()
    let newEntity = menuBar.menuItems["New Entity"]
    XCTAssertTrue(newEntity.waitForExistence(timeout: 2))

    // Actually click it and verify behavior
    newEntity.click()

    // Verify a node was created (check inspector or canvas)
    let elementInspector = app.groups["inspector-element"]
    XCTAssertTrue(elementInspector.waitForExistence(timeout: 2))
}
```

### 2. Keyboard Shortcut Tests
```swift
func testNewEntityShortcut() throws {
    // Create new document first
    app.typeKey("n", modifierFlags: .command)
    sleep(1)

    // Get node count before
    let canvas = app.otherElements["graphCanvas"]

    // Press Cmd+E for new entity
    app.typeKey("e", modifierFlags: .command)
    sleep(1)

    // Verify entity was created via inspector or element count
    let elementInspector = app.groups["inspector-element"]
    XCTAssertTrue(elementInspector.exists, "Element inspector should show selected entity")
}
```

### 3. Canvas Drag Tests (Coordinate-based)
```swift
func testDragNodeOnCanvas() throws {
    let canvas = app.otherElements["graphCanvas"]
    XCTAssertTrue(canvas.waitForExistence(timeout: 5))

    // Create a node first
    app.typeKey("e", modifierFlags: .command)
    sleep(1)

    // Drag from center to offset position
    let startPoint = canvas.coordinate(withNormalizedOffset: CGVector(dx: 0.5, dy: 0.5))
    let endPoint = startPoint.withOffset(CGVector(dx: 100, dy: 50))

    startPoint.press(forDuration: 0.1, thenDragTo: endPoint)
}
```

### 4. Edge Creation by Drag
```swift
func testCreateEdgeByDrag() throws {
    let canvas = app.otherElements["graphCanvas"]

    // Create two nodes
    app.typeKey("e", modifierFlags: .command)
    sleep(1)
    app.typeKey("e", modifierFlags: .command)
    sleep(1)

    // Option+drag from first node to second (edge creation)
    let node1 = app.otherElements["node-1"]  // needs accessibilityIdentifier
    let node2 = app.otherElements["node-2"]

    let start = node1.coordinate(withNormalizedOffset: CGVector(dx: 0.5, dy: 0.5))
    let end = node2.coordinate(withNormalizedOffset: CGVector(dx: 0.5, dy: 0.5))

    // Hold Option while dragging
    start.press(forDuration: 0.1, thenDragTo: end, withVelocity: .default, thenHoldForDuration: 0.1)
}
```

### 5. Inspector Panel Tests
```swift
func testDocumentInspectorFields() throws {
    // Cmd+1 to show Document Inspector
    app.typeKey("1", modifierFlags: .command)
    sleep(1)

    let titleField = app.textFields["document-title"]
    XCTAssertTrue(titleField.waitForExistence(timeout: 2))

    // Edit title
    titleField.click()
    titleField.typeText("My Document")

    // Verify it took
    XCTAssertEqual(titleField.value as? String, "My Document")
}
```

### 6. Undo/Redo Tests
```swift
func testUndoRedoEntity() throws {
    // Create entity
    app.typeKey("e", modifierFlags: .command)
    sleep(1)

    // Verify entity exists
    let inspector = app.groups["inspector-element"]
    XCTAssertTrue(inspector.exists)

    // Undo
    app.typeKey("z", modifierFlags: .command)
    sleep(1)

    // Entity should be gone
    XCTAssertFalse(inspector.exists, "Entity should be removed after undo")

    // Redo
    app.typeKey("z", modifierFlags: [.command, .shift])
    sleep(1)

    // Entity should be back
    XCTAssertTrue(inspector.waitForExistence(timeout: 2), "Entity should return after redo")
}
```

### 7. Copy/Paste Tests
```swift
func testCopyPasteEntity() throws {
    // Create and select entity
    app.typeKey("e", modifierFlags: .command)
    sleep(1)

    // Copy
    app.typeKey("c", modifierFlags: .command)
    sleep(1)

    // Paste
    app.typeKey("v", modifierFlags: .command)
    sleep(1)

    // Should now have 2 nodes
    // Verify via graph state or UI element count
}
```

## Helper Extensions

```swift
extension XCUIElement {
    /// Wait for element and tap
    func waitAndTap(timeout: TimeInterval = 5) {
        XCTAssertTrue(waitForExistence(timeout: timeout))
        tap()
    }

    /// Clear text field
    func clearAndType(_ text: String) {
        guard let stringValue = value as? String else { return }
        tap()
        let deleteString = String(repeating: XCUIKeyboardKey.delete.rawValue, count: stringValue.count)
        typeText(deleteString)
        typeText(text)
    }
}
```

## Running UI Tests

```bash
# Generate Xcode project
xcodegen generate

# Run all UI tests
xcodebuild test -scheme MyApp -destination 'platform=macOS' -only-testing MyAppUITests

# Run specific test
xcodebuild test -scheme MyApp -destination 'platform=macOS' -only-testing MyAppUITests/MyAppUITests/testNewEntityMenuItem

# Or use script
./scripts/run-ui-tests.sh --test testNewEntityMenuItem
```

## Checklist for New Features

When implementing a new feature, ensure:
1. [ ] Accessibility identifier added to all interactive elements
2. [ ] Unit test for model/logic (Swift Testing)
3. [ ] UI test verifying menu item exists
4. [ ] UI test verifying keyboard shortcut works
5. [ ] UI test verifying the action produces expected result
6. [ ] UI test for undo/redo if applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artimath) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
