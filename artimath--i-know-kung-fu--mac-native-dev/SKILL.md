---
name: mac-native-dev
description: This skill should be used when the user asks to "build a mac app", "swift build", "create .app bundle", "code sign", "notarize app", "xcode-free development", or needs guidance on SwiftUI macOS apps, swift-bundler, signing certificates, or production distribution without Xcode GUI. Use when this capability is needed.
metadata:
  author: artimath
---

# Mac Native Development (Xcode-Free)

## Project Structure

```
myapp/
├── Package.swift              # SwiftPM manifest
├── Sources/
│   └── MyApp/
│       ├── MyApp.swift        # @main App entry
│       ├── ContentView.swift
│       └── Resources/
│           └── AppIcon.icns
├── Tests/
│   └── MyAppTests/
├── scripts/
│   ├── package-app.sh         # Bundle creation
│   ├── codesign-app.sh        # Code signing
│   └── notarize.sh            # Notarization
└── dist/                      # Output bundles
```

For multi-platform (iOS + macOS), see [references/build-patterns.md](references/build-patterns.md).

## Two Approaches

### 1. swift-bundler (Simple Projects)
```bash
# Install
swift-bundler --version  # install via: brew tap stackotter/tap && brew install swift-bundler

# Create project
swift bundler create MyApp --template SwiftUI
cd MyApp

# Build & run
swift bundler build
swift bundler run

# Release
swift bundler build -c release -o dist/
```

### 2. Custom Scripts (Production)
More control over bundling, signing, embedded payloads. See [references/build-patterns.md](references/build-patterns.md) for full examples.

```bash
# Build
swift build -c release --product MyApp

# Package (custom script)
./scripts/package-app.sh

# Sign
./scripts/codesign-app.sh dist/MyApp.app

# Notarize
./scripts/notarize.sh dist/MyApp.zip
```

## Package.swift Template

```swift
// swift-tools-version: 6.0
import PackageDescription

let package = Package(
    name: "MyApp",
    platforms: [.macOS(.v15)],
    products: [
        .executable(name: "MyApp", targets: ["MyApp"]),
    ],
    dependencies: [
        // Sparkle for auto-updates (direct distribution)
        .package(url: "https://github.com/sparkle-project/Sparkle", from: "2.8.0"),
    ],
    targets: [
        .executableTarget(
            name: "MyApp",
            dependencies: [
                .product(name: "Sparkle", package: "Sparkle"),
            ],
            resources: [
                .copy("Resources"),
            ],
            swiftSettings: [
                .enableUpcomingFeature("StrictConcurrency"),
            ]),
        .testTarget(
            name: "MyAppTests",
            dependencies: ["MyApp"]),
    ])
```

## Hot Reload (InjectionNext)

```swift
import Inject

@main
struct MyApp: App {
    @ObserveInjection var inject
    var body: some Scene {
        WindowGroup {
            ContentView()
                .enableInjection()
        }
    }
}
```

**Xcode 16.3+ fix**: Add `EMIT_FRONTEND_COMMAND_LINES = YES` to build settings.

## Architecture Standards

### Swift 6 / macOS 15+
- Use `@Observable` (not ObservableObject)
- Use `async/await` (not DispatchQueue)
- Use structured concurrency (actors, task groups)

### State Management
```swift
@Observable
class AppState {
    var items: [Item] = []
    var selectedItem: Item?
}

struct ContentView: View {
    @State private var state = AppState()
    var body: some View {
        // state.items triggers re-render only when accessed
    }
}
```

### Multi-Window App
```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }

        Window("Settings", id: "settings") {
            SettingsView()
        }
        .keyboardShortcut(",", modifiers: .command)

        MenuBarExtra("MyApp", systemImage: "star") {
            MenuBarView()
        }
    }
}
```

## Code Signing (Production)

### Auto-Select Identity
```bash
# Find available identities
security find-identity -p codesigning -v

# Priority: Developer ID Application > Apple Distribution > Apple Development
```

### Sign with Entitlements
```bash
# Create entitlements file
cat > entitlements.plist <<'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.app-sandbox</key>
    <false/>
    <key>com.apple.security.automation.apple-events</key>
    <true/>
</dict>
</plist>
EOF

# Clear extended attributes
xattr -cr MyApp.app

# Sign embedded frameworks first (inside-out)
codesign --force --options runtime --timestamp \
  --sign "Developer ID Application: Name (TEAMID)" \
  MyApp.app/Contents/Frameworks/*.framework

# Sign main binary
codesign --force --options runtime --timestamp \
  --entitlements entitlements.plist \
  --sign "Developer ID Application: Name (TEAMID)" \
  MyApp.app/Contents/MacOS/MyApp

# Sign bundle
codesign --force --options runtime --timestamp \
  --entitlements entitlements.plist \
  --sign "Developer ID Application: Name (TEAMID)" \
  MyApp.app
```

### Notarization
```bash
# Store credentials (one-time)
xcrun notarytool store-credentials "myprofile" \
  --apple-id you@email.com \
  --team-id TEAMID \
  --password @keychain:notarytool

# Create zip
ditto -c -k --keepParent MyApp.app MyApp.zip

# Submit and wait
xcrun notarytool submit MyApp.zip --keychain-profile "myprofile" --wait

# Staple
xcrun stapler staple MyApp.app
```

## SwiftUI Gaps (AppKit Bridges)

| Feature | Solution |
|---------|----------|
| MenuBarExtra + Settings | Hidden windows, scene ordering tricks |
| NSEvent handling | NSViewRepresentable bridge |
| First responder | AppKit bridge |
| Complex menus | NSMenu via AppKit |

```swift
struct AppKitTextView: NSViewRepresentable {
    @Binding var text: String

    func makeNSView(context: Context) -> NSTextView {
        let textView = NSTextView()
        textView.delegate = context.coordinator
        return textView
    }

    func updateNSView(_ nsView: NSTextView, context: Context) {
        nsView.string = text
    }

    func makeCoordinator() -> Coordinator { Coordinator(self) }

    class Coordinator: NSObject, NSTextViewDelegate {
        var parent: AppKitTextView
        init(_ parent: AppKitTextView) { self.parent = parent }
        func textDidChange(_ notification: Notification) {
            guard let tv = notification.object as? NSTextView else { return }
            parent.text = tv.string
        }
    }
}
```

## Data Layer

### SwiftData
```swift
import SwiftData

@Model
class Item {
    var title: String
    var createdAt: Date
    init(title: String) {
        self.title = title
        self.createdAt = .now
    }
}

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup { ContentView() }
            .modelContainer(for: Item.self)
    }
}
```

### Networking
```swift
func fetchData() async throws -> [Item] {
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([Item].self, from: data)
}
```

## Testing

### Swift Testing Framework
```swift
import Testing

@Test func itemCreation() {
    let item = Item(title: "Test")
    #expect(item.title == "Test")
}

@Test("Rename variants", arguments: ["New", "Updated", ""])
func itemRename(newTitle: String) {
    let item = Item(title: "Original")
    item.title = newTitle
    #expect(item.title == newTitle)
}
```

```bash
swift test
swift test --filter ItemTests
```

## Distribution

### Direct (Recommended for Indie)
1. Developer ID ($99/year)
2. Code sign + notarize
3. Sparkle for auto-updates
4. Distribute via website/DMG

### App Store
- Requires Xcode for xcarchive
- 15-30% commission
- Better for consumer discovery

## Additional Resources

### References
- [references/install.md](references/install.md) - Environment setup & additional skills
- [references/build-patterns.md](references/build-patterns.md) - Production build scripts (signing, notarization)
- [references/automation.md](references/automation.md) - Mac automation (screen capture, GUI control, MCP)
- [references/sparkle-scaffold.md](references/sparkle-scaffold.md) - Sparkle update system setup
- [references/release-playbook.md](references/release-playbook.md) - Complete release workflow
- [ai-workflow.md](ai-workflow.md) - AI-assisted development tips

### Scripts
Use scripts in `scripts/` for executable templates. See `references/install.md` for usage.

## MCP Servers

Project-local MCP config at `.mcp.json`:

```json
{
  "mcpServers": {
    "peekaboo": {
      "command": "npx",
      "args": ["-y", "@steipete/peekaboo"]
    }
  }
}
```

Peekaboo provides screen capture and GUI automation tools to Claude.

## Troubleshooting

### Signing Identity Not Found
```bash
security find-identity -p codesigning -v
# If empty, need Apple Developer account + certificates
```

### Notarization Rejected
```bash
# Check detailed log
xcrun notarytool log <submission-id> --keychain-profile "myprofile"
```

### Gatekeeper Blocks App
```bash
# Verify signature
codesign -vvv --deep --strict MyApp.app
spctl -a -vv MyApp.app
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artimath) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
