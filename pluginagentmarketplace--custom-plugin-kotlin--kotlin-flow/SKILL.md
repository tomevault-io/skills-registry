---
name: kotlin-flow
description: Kotlin Flow - StateFlow, SharedFlow, operators, testing Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Kotlin Flow Skill

Reactive programming with Kotlin Flow.

## Topics Covered

### Cold vs Hot Flows
```kotlin
// Cold Flow - starts fresh for each collector
fun loadData(): Flow<Data> = flow {
    emit(fetchData())
}

// Hot Flow - shared state
private val _state = MutableStateFlow(State())
val state: StateFlow<State> = _state.asStateFlow()
```

### Flow Operators
```kotlin
fun searchUsers(query: Flow<String>): Flow<List<User>> =
    query
        .debounce(300)
        .filter { it.length >= 2 }
        .distinctUntilChanged()
        .flatMapLatest { term -> userRepository.search(term) }
        .catch { emit(emptyList()) }
```

### Combining Flows
```kotlin
val dashboard: Flow<Dashboard> = combine(
    userFlow,
    ordersFlow,
    notificationsFlow
) { user, orders, notifications ->
    Dashboard(user, orders.size, notifications.count())
}
```

### Testing with Turbine
```kotlin
@Test
fun `flow emits values`() = runTest {
    viewModel.state.test {
        assertThat(awaitItem().isLoading).isFalse()
        viewModel.load()
        assertThat(awaitItem().isLoading).isTrue()
        advanceUntilIdle()
        assertThat(awaitItem().data).isNotNull()
    }
}
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| Flow never emits | Add terminal operator (collect, first) |
| Stale data in UI | Use stateIn or shareIn properly |

## Usage
```
Skill("kotlin-flow")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
