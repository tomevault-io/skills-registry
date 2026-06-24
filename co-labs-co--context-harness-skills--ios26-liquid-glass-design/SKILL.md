---
name: ios26-liquid-glass-design
description: Apple's Liquid Glass design system for iOS 26+ with backward-compatible fallbacks for iOS 17-25. Use this skill when implementing glass effects, translucent buttons, adaptive glass styling, or building UIs that gracefully degrade across iOS versions. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# iOS 26 Liquid Glass Design Pattern

Implement Apple's Liquid Glass design system introduced in iOS 26 (WWDC25) with graceful fallbacks for iOS 17-25. This skill covers adaptive glass styling, button styles, container effects, and the architectural patterns for building version-aware SwiftUI components.

## Design Principles (WWDC25 Sessions 219, 323, 356)

### Core Guidelines

1. **Content-hugging shapes**: Glass buttons should be capsule-shaped, NOT full-width
2. **Hierarchy through prominence**: 
   - Primary actions → `.glassProminent` with tinting
   - Secondary actions → `.glass` without tinting
3. **Never stack glass on glass**: Avoid layering multiple glass effects
4. **Capsule is default**: Large/Extra-Large buttons automatically use capsule shapes

### Visual Hierarchy

| Element Type | iOS 26 Style | Fallback Style |
|--------------|--------------|----------------|
| Primary action | `.buttonStyle(.glassProminent)` | Filled capsule with color |
| Secondary action | `.buttonStyle(.glass)` | Semi-transparent capsule |
| Status badge | `.glassEffect()` | `Color.black.opacity(0.6)` capsule |
| Card container | `.glassEffect()` | `Color.black.opacity(0.7)` rounded rect |

## Architecture Pattern

### The Availability Check Problem

Direct `if #available` checks in SwiftUI `body` cause performance issues due to view diffing. Instead, use wrapper components with separate iOS 26+ and fallback implementations.

```swift
// ❌ AVOID: if #available in body causes performance issues
struct BadButton: View {
    var body: some View {
        if #available(iOS 26.0, *) {
            Button("Tap") { }
                .buttonStyle(.glass)  // Recomputed every render
        } else {
            Button("Tap") { }
                .background(Capsule().fill(.black.opacity(0.6)))
        }
    }
}

// ✅ CORRECT: Wrapper component with separate implementations
struct GlassButton: View {
    let title: String
    let action: () -> Void
    
    var body: some View {
        if #available(iOS 26.0, *) {
            GlassButtoniOS26(title: title, action: action)
        } else {
            GlassButtonFallback(title: title, action: action)
        }
    }
}
```

## View Extension for Adaptive Glass

### Basic Glass Effect

```swift
extension View {
    /// Applies glass effect on iOS 26+, or semi-transparent capsule on older versions
    /// Use for badges, status indicators, and small UI elements
    @ViewBuilder
    func adaptiveGlass() -> some View {
        if #available(iOS 26.0, *) {
            self.glassEffect()
        } else {
            self.background(Capsule().fill(Color.black.opacity(0.6)))
        }
    }
    
    /// Applies glass effect with rounded rectangle on iOS 26+, or fallback styling
    /// Use for cards and larger container elements
    @ViewBuilder
    func adaptiveGlassCard() -> some View {
        if #available(iOS 26.0, *) {
            self.glassEffect()
        } else {
            self.background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(Color.black.opacity(0.7))
            )
        }
    }
}
```

### Usage

```swift
// Status badge with glass effect
Text("LIVE")
    .font(.caption.bold())
    .foregroundColor(.white)
    .padding(.horizontal, 12)
    .padding(.vertical, 6)
    .adaptiveGlass()

// Card with glass effect
VStack {
    Text("Stream Info")
    Text("1920x1080 @ 30fps")
}
.padding()
.adaptiveGlassCard()
```

## Glass Button Components

### Primary Action Button (GlassProminent)

```swift
/// Primary action button with glass prominent style (iOS 26+)
@available(iOS 26.0, *)
struct GlassProminentButtoniOS26: View {
    let title: String
    let icon: String
    var tintColor: Color = .blue
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            Label(title, systemImage: icon)
                .font(.title2.bold())
                .padding(.horizontal, 8)
                .padding(.vertical, 4)
        }
        .buttonStyle(.glassProminent)
        .tint(tintColor)
    }
}

/// Primary action button fallback (iOS 17-25)
struct GlassProminentButtonFallback: View {
    let title: String
    let icon: String
    var tintColor: Color = .blue
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: icon)
                    .font(.title2)
                Text(title)
                    .font(.title3.bold())
            }
            .foregroundColor(.white)
            .padding(.horizontal, 28)
            .padding(.vertical, 18)
            .background(
                Capsule()
                    .fill(tintColor.opacity(0.85))
            )
        }
    }
}

/// Wrapper that chooses the right implementation
struct GlassProminentButton: View {
    let title: String
    let icon: String
    var tintColor: Color = .blue
    let action: () -> Void
    
    var body: some View {
        if #available(iOS 26.0, *) {
            GlassProminentButtoniOS26(title: title, icon: icon, tintColor: tintColor, action: action)
        } else {
            GlassProminentButtonFallback(title: title, icon: icon, tintColor: tintColor, action: action)
        }
    }
}
```

### Secondary Action Button (Glass)

```swift
/// Secondary action button with glass style (iOS 26+)
@available(iOS 26.0, *)
struct GlassButtoniOS26: View {
    let title: String
    let icon: String
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            Label(title, systemImage: icon)
                .font(.title3.weight(.semibold))
                .padding(.horizontal, 6)
                .padding(.vertical, 2)
        }
        .buttonStyle(.glass)
    }
}

/// Secondary action button fallback (iOS 17-25)
struct GlassButtonFallback: View {
    let title: String
    let icon: String
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: icon)
                    .font(.title3)
                Text(title)
                    .font(.title3.weight(.semibold))
            }
            .foregroundColor(.white)
            .padding(.horizontal, 24)
            .padding(.vertical, 16)
            .background(
                Capsule()
                    .fill(Color.black.opacity(0.6))
            )
        }
    }
}

/// Wrapper that chooses the right implementation
struct GlassButton: View {
    let title: String
    let icon: String
    let action: () -> Void
    
    var body: some View {
        if #available(iOS 26.0, *) {
            GlassButtoniOS26(title: title, icon: icon, action: action)
        } else {
            GlassButtonFallback(title: title, icon: icon, action: action)
        }
    }
}
```

## Full-Width Bottom Button

For full-width primary actions (like Start/Stop streaming), use `.ultraThinMaterial` with a color overlay instead of pure glass:

```swift
struct FullWidthBottomButton: View {
    let title: String
    let icon: String
    var tintColor: Color = .blue
    var isLoading: Bool = false
    let action: () -> Void
    
    private var isLargeScreen: Bool {
        UIDevice.current.userInterfaceIdiom == .pad
    }
    
    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: icon)
                    .font(.title2)
                Text(title)
                    .font(.title3.bold())
            }
            .foregroundColor(.white)
            .frame(maxWidth: isLargeScreen ? 300 : .infinity)
            .padding(.vertical, 18)
            .padding(.horizontal, 24)
            .background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(.ultraThinMaterial)
                    .overlay(
                        RoundedRectangle(cornerRadius: 16)
                            .fill(tintColor.opacity(0.3))
                    )
            )
        }
        .disabled(isLoading)
        .padding(.horizontal, isLargeScreen ? 0 : 16)
        .padding(.bottom, isLargeScreen ? 40 : 24)
    }
}
```

## CI/CD Considerations

### The Xcode Version Problem

GitHub Actions and most CI services use stable Xcode versions. iOS 26 SDK requires Xcode 26, which may not be available in CI when iOS 26 is first released.

### Conditional Compilation Strategy

```swift
// Option 1: Runtime availability check (always compiles)
if #available(iOS 26.0, *) {
    self.glassEffect()
}

// Option 2: Compiler version check (for APIs that don't exist in older SDKs)
#if compiler(>=6.2)  // Swift 6.2 = Xcode 26
    .buttonStyle(.glassProminent)
#else
    .buttonStyle(.borderedProminent)
#endif
```

### Recommended Approach

Use runtime `#available` checks that compile on any Xcode version, with fallback implementations that look good on iOS 17-25:

```swift
// This compiles on Xcode 15/16 AND Xcode 26
// On iOS 26+: Uses native glass effect
// On iOS 17-25: Uses semi-transparent capsule
@ViewBuilder
func adaptiveGlass() -> some View {
    if #available(iOS 26.0, *) {
        self.glassEffect()
    } else {
        self.background(Capsule().fill(Color.black.opacity(0.6)))
    }
}
```

## Status Badge Example

```swift
struct StatusBadge: View {
    let isLive: Bool
    let text: String
    
    var body: some View {
        HStack(spacing: 6) {
            Circle()
                .fill(isLive ? Color.red : Color.gray)
                .frame(width: 8, height: 8)
            Text(text)
                .font(.caption.bold())
                .foregroundColor(.white)
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 6)
        .adaptiveGlass()
    }
}

// Usage
StatusBadge(isLive: true, text: "LIVE")
StatusBadge(isLive: false, text: "OFFLINE")
```

## Card Container Example

```swift
struct InfoCard: View {
    let title: String
    let value: String
    
    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            Text(title)
                .font(.caption)
                .foregroundColor(.secondary)
            Text(value)
                .font(.headline)
                .foregroundColor(.primary)
        }
        .padding()
        .frame(maxWidth: .infinity, alignment: .leading)
        .adaptiveGlassCard()
    }
}
```

## Complete Usage Example

```swift
struct StreamingControlView: View {
    @State private var isStreaming = false
    
    var body: some View {
        ZStack {
            // Camera preview background
            CameraPreviewView()
            
            VStack {
                // Top status bar with glass badges
                HStack {
                    StatusBadge(isLive: isStreaming, text: isStreaming ? "LIVE" : "READY")
                    Spacer()
                    
                    // Settings button with glass style
                    GlassButton(title: "Settings", icon: "gear") {
                        // Open settings
                    }
                }
                .padding()
                
                Spacer()
                
                // Bottom action button
                if isStreaming {
                    GlassProminentButton(
                        title: "Stop Streaming",
                        icon: "stop.fill",
                        tintColor: .red
                    ) {
                        isStreaming = false
                    }
                } else {
                    GlassProminentButton(
                        title: "Start Streaming",
                        icon: "play.fill",
                        tintColor: .green
                    ) {
                        isStreaming = true
                    }
                }
            }
        }
    }
}
```

## Fallback Color Recommendations

| Use Case | Fallback Background |
|----------|---------------------|
| Dark overlay (camera UI) | `Color.black.opacity(0.6)` |
| Card background | `Color.black.opacity(0.7)` |
| Light mode badge | `Color.white.opacity(0.8)` |
| Tinted primary button | `tintColor.opacity(0.85)` |

## Testing Checklist

- [ ] Test on iOS 26 simulator/device with native glass effects
- [ ] Test on iOS 17-25 with fallback styling
- [ ] Verify no visual glitches when switching between versions
- [ ] Check dark mode and light mode appearance
- [ ] Validate contrast ratios for accessibility
- [ ] Test on iPad (larger touch targets, different layouts)

## Common Mistakes to Avoid

### 1. Stacking Glass on Glass

```swift
// ❌ DON'T: Multiple glass layers
VStack {
    Text("Hello")
        .adaptiveGlass()
}
.adaptiveGlassCard()

// ✅ DO: Single glass layer
VStack {
    Text("Hello")
}
.adaptiveGlassCard()
```

### 2. Full-Width Glass Buttons

```swift
// ❌ DON'T: Glass buttons shouldn't be full-width
Button("Start") { }
    .frame(maxWidth: .infinity)
    .buttonStyle(.glass)

// ✅ DO: Content-hugging capsule shape
Button("Start") { }
    .buttonStyle(.glass)  // Capsule is default
```

### 3. Missing Availability Checks

```swift
// ❌ DON'T: Will crash on iOS 17-25
Button("Tap") { }
    .buttonStyle(.glass)

// ✅ DO: Always check availability
if #available(iOS 26.0, *) {
    Button("Tap") { }
        .buttonStyle(.glass)
} else {
    Button("Tap") { }
        .background(Capsule().fill(.black.opacity(0.6)))
}
```

## Reference Implementation

See `CarSeet/CarSeet/ContentView.swift` for the complete production implementation including:

- `adaptiveGlass()` and `adaptiveGlassCard()` view extensions
- `GlassProminentButton` and `GlassButton` wrapper components
- `FullWidthBottomButton` for streaming controls
- Status badges and info cards with glass effects

## WWDC25 Sessions

- **Session 219**: Introduction to Liquid Glass
- **Session 323**: Designing with Liquid Glass
- **Session 356**: Implementing Liquid Glass in SwiftUI

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
