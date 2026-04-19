---
name: compose-check
description: Jetpack Compose best practices check. Inspects recomposition, state management, side effects, and performance. Use when this capability is needed.
metadata:
  author: riku-kano
---

# Compose Best Practices Check

Perform a detailed check of Jetpack Compose code based on official best practices.

## Checklist

### 1. State Management
- [ ] State hoisting: Are Composables stateless (state passed from callers)?
- [ ] Is `remember` used to cache expensive computations?
- [ ] Is `rememberSaveable` used for state that must survive configuration changes?
- [ ] Is `derivedStateOf` used to compute derived state efficiently?
- [ ] Is ViewModel state collected with `collectAsStateWithLifecycle()`?
- [ ] Is UI State defined as an immutable `data class`?

### 2. Recomposition Efficiency
- [ ] **No backwards writes**: No state mutation during composition
- [ ] **Stable types**: `@Stable` / `@Immutable` annotations where needed
- [ ] **Lambda stability**: Event handler lambdas not recreated on every recomposition
- [ ] **Lazy layout keys**: `items(key = { ... })` provides stable unique keys
- [ ] **Deferred state reads**: Lambda-based modifiers (`Modifier.drawBehind {}`) to defer reads to the draw phase
- [ ] No unnecessary recompositions triggered by parameter changes

### 3. Side Effects
Verify each Side Effect API is used correctly:

| API | Correct Usage |
|-----|--------------|
| `LaunchedEffect(key)` | Launches coroutine on key change. Is the key correct? |
| `DisposableEffect(key)` | Register/unregister resources. Is `onDispose` cleaning up properly? |
| `rememberCoroutineScope` | Launch coroutines from event handlers |
| `rememberUpdatedState` | Reference latest value in long-lived effects |
| `SideEffect` | Publish Compose state to non-Compose code after every successful recomposition |
| `produceState` | Convert non-Compose state sources into Compose State |
| `snapshotFlow` | Convert Compose State to Flow |

- Are there any operations outside Composable scope inside side effects?
- Is `LaunchedEffect(Unit)` truly a one-time operation?

### 4. Navigation
- [ ] Is `NavController` NOT passed to child Composables (should use callback pattern)?
- [ ] Are routes defined as `@Serializable` type-safe classes?
- [ ] Is deep link handling correct?

### 5. Performance
- [ ] No heavy work inside Composition (IO, network, DB)
- [ ] Are `LazyColumn`/`LazyRow` used appropriately for large lists?
- [ ] Is image loading optimized (Coil/Glide + caching)?
- [ ] No unnecessary `Modifier` chain recreation

### 6. Accessibility & Testing
- [ ] Is `contentDescription` set on interactive elements?
- [ ] Are `semantics` properly configured?
- [ ] Are `@Preview` functions provided (multiple state variations)?
- [ ] Are `testTag` values set where needed for testing?

## Output Format

```
### [Critical/Warning/Info] file:line-number
**Check Item**: Applicable checklist item
**Issue**: Specific problem description
**Fix**: Code example
```

End with a Compose quality score (A/B/C/D) and prioritized improvement list.

## Target

If $ARGUMENTS is specified, check that file/directory.
If not specified, check all Composable files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riku-kano) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
