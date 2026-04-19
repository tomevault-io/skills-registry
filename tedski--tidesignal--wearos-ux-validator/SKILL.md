---
name: wearos-ux-validator
description: Validate UI/UX compliance with WearOS design guidelines and TideSignal's established patterns for optimal user experience on small circular screens Use when this capability is needed.
metadata:
  author: tedski
---

# WearOS UX Validator

**Invoke with**: `/wearos-ux-validator [feature description]`

## Purpose

Validate UI/UX compliance with WearOS design guidelines and TideSignal's established patterns for optimal user experience on small circular screens.

## Baseline Patterns (Current Codebase)

### Scrollable Content
```kotlin
// CORRECT: ScalingLazyColumn for rotary input support
ScalingLazyColumn(
    modifier = Modifier.fillMaxSize(),
    verticalArrangement = Arrangement.spacedBy(8.dp)
) { /* items */ }

// WRONG: LazyColumn (loses rotary input)
LazyColumn { /* items */ }
```

### Interactive Elements
```kotlin
// CORRECT: Chip for interactive elements
Chip(
    onClick = { /* action */ },
    label = { Text("Action") }
)

// WRONG: Button (less optimized for WearOS)
Button(onClick = { /* action */ }) { Text("Action") }
```

### Information Containers
```kotlin
// CORRECT: Card for information display
Card { /* content */ }
```

### Typography Scale
- **`display1`**: 40sp - Primary data (current tide height, time to next tide)
- **`title2`**: 20sp - Section headers
- **`body2`**: 14sp - Body content, descriptions
- **`caption1`**: 12sp - Metadata (station name, date)
- **`caption2`**: 10sp - Smallest text (disclaimers, fine print)

### Spacing Standards
- **Item spacing**: 8dp vertical in lists
- **Padding**: 10-12dp for content containers
- **Icon size**: 24dp standard Material icons

### Dual Theming
```kotlin
// Active mode: Full color
val backgroundColor = MaterialTheme.colors.background

// AOD mode: High-contrast black/white
if (ambientState.isAmbient) {
    // Simplified, high-contrast UI
}
```

### Navigation Pattern
```kotlin
SwipeDismissableNavHost(
    startDestination = "main",
    navController = navController
) {
    composable("main") { MainScreen() }
    composable("detail") { DetailScreen() }
}
```

### State Management
```kotlin
// StateFlow collection
val uiState by viewModel.uiState.collectAsState()

// Remember for expensive calculations
val processedData = remember(key1) {
    expensiveCalculation(key1)
}
```

## Warning Triggers

⚠️ **Wrong Scrollable**: Using `LazyColumn` instead of `ScalingLazyColumn`
⚠️ **Wrong Interactive**: Using `Button` instead of `Chip` for primary actions
⚠️ **Small Touch Targets**: Interactive elements <48dp (accessibility concern)
⚠️ **Custom Fonts**: Using non-standard fonts (consistency issue)
⚠️ **Wrong Typography Scale**: Font sizes not matching established scale
⚠️ **Missing AOD Support**: No ambient mode handling for always-visible features
⚠️ **Wrong Navigation**: Not using `SwipeDismissableNavHost`
⚠️ **Blocking UI**: Main thread operations without loading states
⚠️ **Missing Loading States**: No feedback during async operations
⚠️ **Inefficient Recomposition**: Missing `remember`, unstable keys
⚠️ **Poor Contrast**: Colors not readable on round screen or in AOD

## Checking Process

When UI code is proposed or implemented:

1. **Review Scrollable Composables**
   - Check for `LazyColumn`, `LazyRow` → Should be `ScalingLazyColumn`
   - Verify rotary input support

2. **Check Interactive Elements**
   - Buttons → Should be Chips for WearOS
   - Verify touch targets ≥48dp
   - Check for proper click feedback

3. **Validate Typography**
   - Measure against established scale
   - Ensure readability on small circular screen
   - Check no custom fonts introduced

4. **Verify Spacing/Padding**
   - Item spacing matches 8dp standard
   - Content padding 10-12dp
   - Icons at 24dp

5. **Ensure AOD Support**
   - Check for `ambientState.isAmbient` handling
   - Verify simplified UI in ambient mode
   - Confirm high-contrast colors

6. **Check Navigation**
   - Verify `SwipeDismissableNavHost` usage
   - Test back navigation with swipe-to-dismiss
   - Ensure proper navigation graph

7. **Validate State Management**
   - Check for `collectAsState()` on flows
   - Verify `remember` for expensive operations
   - Ensure stable keys for collections

## Output Format

```markdown
## WearOS UX Validator Analysis

### ✅ Compliant Patterns
- [List of correct WearOS patterns being used]

### ⚠️ Warnings
- **Issue**: [Description of UX concern]
  - **Pattern**: [Current TideSignal approach]
  - **Proposed**: [What's being introduced]
  - **Impact**: [User experience consequence]
  - **Recommendation**: [How to fix]

### 📊 Summary
**WearOS compliance**: HIGH/MEDIUM/LOW
**Critical issues**: [Count]
**Next steps**: [Recommendations]
```

## Example Analysis

**Feature**: "Add scrollable list of favorite stations"

### ✅ Compliant Patterns
- Using ScalingLazyColumn for scrollable list
- Chip elements for station selection
- Typography matches scale (title2 for station names, caption1 for location)
- 8dp spacing between items

### ⚠️ Warnings
- **Issue**: Missing AOD optimization
  - **Pattern**: TideSignal supports ambient mode with reduced updates
  - **Proposed**: Favorites list doesn't check ambientState
  - **Impact**: Full-color list shown in AOD, drains battery and looks out of place
  - **Recommendation**: Add ambient state check and simplify list in AOD (reduce colors, show fewer items)

- **Issue**: No loading state during station load
  - **Pattern**: App shows loading indicators during async operations
  - **Proposed**: Favorites list appears blank during initial load
  - **Impact**: User sees empty screen, unclear if loading or no favorites
  - **Recommendation**: Add CircularProgressIndicator or skeleton loading state

### 📊 Summary
**WearOS compliance**: MEDIUM
**Critical issues**: 1 (missing AOD support)
**Next steps**: Add ambient mode handling and loading states

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tedski) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
