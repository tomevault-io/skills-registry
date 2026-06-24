---
name: swift-review
description: This skill should be used when the user asks to "review my Swift code", "analyze this macOS app", "find bugs in my Swift project", "check for memory leaks", "audit threading safety", "check accessibility compliance", "review my iOS app", or wants a comprehensive code review of a Swift macOS or iOS application. Launches parallel analysis agents to identify critical issues, state management bugs, UI/UX problems, and accessibility gaps. Use when this capability is needed.
metadata:
  author: adairrr
---

# Swift Code Review

Perform comprehensive code reviews for Swift macOS/iOS applications by launching parallel exploration agents and producing a prioritized findings report with actionable fix plans.

## When to Use

Trigger this skill when:
- Reviewing a Swift codebase for quality issues
- Auditing an app before release
- Investigating crashes, memory leaks, or race conditions
- Checking accessibility compliance
- Validating platform version compatibility

## Review Process

### Phase 0: Project Discovery

Before launching agents, quickly assess the project to customize the review:

1. Check for `Package.swift`, `project.yml`, or `*.xcodeproj` to understand build system
2. Identify deployment target (iOS vs macOS, minimum version)
3. Detect frameworks in use (CoreAudio, CoreData, CloudKit, HealthKit, etc.)
4. Note app type (standard app, menu bar app, widget, extension)

This determines which specialized checks to emphasize.

### Phase 1: Launch Parallel Analysis Agents

Launch **6 exploration agents in parallel** using the Task tool with `subagent_type=Explore`. Each agent analyzes a specific aspect of the codebase.

**Agent prompts to use:**

1. **System Frameworks & Native APIs Agent**
```
Analyze system framework and native API usage in this Swift project. Search for:
- C/Objective-C API bridging (AudioObject*, AVAudioSession, CoreData, HealthKit, etc.)
- Callback/delegate registration without cleanup (observers, listeners, delegates)
- Status/error codes ignored (OSStatus, NSError, Result types with let _ =)
- CF types memory handling (Unmanaged, CFRetain/CFRelease, takeRetainedValue)
- Callbacks/delegates performing work on unexpected threads
- Classes registering system callbacks without deinit cleanup
- Notification center observers without removal
- KVO observations without invalidation
Report file:line references for each finding.
```

2. **Threading & Concurrency Agent**
```
Analyze threading and concurrency patterns in this Swift project. Search for:
- @MainActor usage and potential violations
- Properties accessed from multiple threads without synchronization
- DispatchQueue patterns (sync vs async correctness, deadlock risks)
- Task/async-await spawning without cancellation tracking
- Race conditions in shared mutable state
- Actor isolation issues
- UserDefaults/Keychain thread safety
- Singleton patterns and their thread safety
- Combine publisher thread safety
Report file:line references for each finding.
```

3. **State Management Agent**
```
Analyze state management and persistence in this Swift project. Search for:
- SwiftUI: @State, @StateObject, @ObservedObject, @Published, @Environment patterns
- Local @State copies that could become stale vs source of truth
- Persistence: UserDefaults, CoreData, FileManager, Keychain usage
- Codable encoding/decoding with try? (silent failures)
- Data sync issues between views and managers/stores
- ObservableObject/Observable property publishing problems
- Binding misuse or unnecessary state duplication
Report file:line references for each finding.
```

4. **UI & UX Patterns Agent**
```
Analyze UI and UX patterns in this Swift project. Search for:
- Controls updating on every change vs debounced/on release
- Missing loading states for async operations
- Errors logged to console but not shown to users
- Force unwraps (!) in UI code that could crash
- Main thread blocking (sync network calls, heavy computation)
- Navigation state management issues
- Modal/sheet presentation edge cases
- Platform-specific: Menu bar apps (MenuBarExtra), widgets, extensions
Report file:line references for each finding.
```

5. **Platform Compatibility Agent**
```
Analyze platform version compatibility in this Swift project. Search for:
- API availability vs deployment target mismatches
- onChange(of:) syntax differences (iOS 17+/macOS 14+ uses two params)
- @Environment property availability by version
- @Observable vs ObservableObject (iOS 17+/macOS 14+)
- @available annotations correctness and completeness
- Deprecated APIs without migration path
- Platform-specific code (#if os(macOS)) correctness
Report file:line references for each finding.
```

6. **Accessibility Agent**
```
Analyze accessibility compliance in this Swift project. Search for:
- .labelsHidden() without .accessibilityLabel()
- Interactive controls (Button, Toggle, Slider, Picker) missing accessibility labels
- Images without .accessibilityLabel() or not marked .accessibilityHidden()
- Missing .accessibilityValue() on controls with dynamic values
- Missing .accessibilityHint() for non-obvious interactions
- Custom controls without proper accessibility traits
- Color-only information (needs text/icon alternative)
- Touch target sizes (minimum 44x44 points)
Report file:line references for each finding.
```

### Phase 2: Compile Findings

After all agents complete, organize findings by severity:

**Critical** - Crashes, memory leaks, data loss:
- Memory leaks (unreleased observers, retain cycles)
- Race conditions causing crashes
- Force unwraps that can fail at runtime
- Data corruption or loss
- Undefined behavior

**High-Severity** - State bugs, silent failures:
- State synchronization problems
- Silent failures (swallowed errors)
- Thread safety violations
- Poor error recovery
- Security issues (hardcoded secrets, insecure storage)

**Medium** - UI/UX, accessibility:
- VoiceOver/accessibility failures
- Confusing user experience
- Missing user feedback
- Performance issues
- Platform guideline violations

**Low** - Code quality:
- Style inconsistencies
- Minor optimizations
- Documentation gaps

### Phase 3: Generate Implementation Plan

For each issue, provide:
1. Specific file:line references
2. Code snippets showing fix approach
3. Dependency order (which fixes must come first)
4. Testing steps to verify

## Output Format

Structure the final report as:

```markdown
# Code Review: [Project Name]

## Project Profile
- **Platform:** macOS/iOS/multiplatform
- **Deployment Target:** [version]
- **App Type:** Standard app / Menu bar / Widget / Extension
- **Key Frameworks:** [detected frameworks]

## Summary
- Critical: X issues
- High: X issues
- Medium: X issues
- Low: X issues

## Critical Issues

### 1. [Issue Title]
**File:** `path/to/file.swift:XX-YY`
**Problem:** [Description]
**Impact:** [Consequences]
**Fix:**
[Code snippet or steps]

## High-Severity Issues
[Same format]

## Medium Issues
[Same format]

## Low Issues
[Brief descriptions]

## Files to Modify
| File | Changes |
|------|---------|
| file.swift | Description |

## Implementation Order
1. **[Fix Name]** (blocks: 2, 3)
2. **[Fix Name]** (depends on: 1)

## Verification Plan
### Phase 1 (Critical)
- [ ] Test step
### Phase 2 (High)
- [ ] Test step
```

## Additional Resources

### Reference Files
- **`references/checklist.md`** - Complete Swift verification checklist
- **`references/patterns.md`** - Common issue patterns and fix approaches
- **`references/frameworks.md`** - Framework-specific guidance (CoreAudio, CoreData, etc.)

### Example Reports
- **`examples/sample-report.md`** - Example of a completed review report

---
> Source: [adairrr/macos-volume-guard](https://github.com/adairrr/macos-volume-guard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
