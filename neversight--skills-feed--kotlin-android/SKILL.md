---
name: kotlin-android
description: Modern Android development - Jetpack, Compose, Architecture Components Use when this capability is needed.
metadata:
  author: neversight
---

# Kotlin Android Skill

Production-ready Android development with Jetpack libraries.

## Topics Covered

### MVVM Pattern
```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow(UiState())
    val uiState = _uiState.asStateFlow()

    fun load() = viewModelScope.launch {
        _uiState.update { it.copy(isLoading = true) }
        repository.getUsers()
            .onSuccess { users -> _uiState.update { it.copy(users = users, isLoading = false) } }
    }
}
```

### Compose UI
```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel = hiltViewModel()) {
    val state by viewModel.uiState.collectAsStateWithLifecycle()
    UserContent(state)
}
```

### Type-Safe Navigation
```kotlin
@Serializable
data class ProfileRoute(val userId: String)

composable<ProfileRoute> { entry ->
    val route: ProfileRoute = entry.toRoute()
}
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| Recomposition loop | Mark class @Stable or use derivedStateOf |
| ViewModel recreated | Use hiltViewModel() not viewModel() |

## Usage
```
Skill("kotlin-android")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
