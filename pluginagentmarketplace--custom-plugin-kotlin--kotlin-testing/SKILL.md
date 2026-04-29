---
name: kotlin-testing
description: Kotlin testing - JUnit 5, MockK, Kotest, coroutine testing Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Kotlin Testing Skill

Comprehensive testing with JUnit 5, MockK, and coroutine testing.

## Topics Covered

### MockK
```kotlin
class UserServiceTest {
    private val repository: UserRepository = mockk()
    private val service = UserService(repository)

    @Test
    fun `createUser saves and returns`() {
        every { repository.save(any()) } returns User(1, "test@test.com")

        val result = service.create(CreateUserRequest("test@test.com"))

        assertThat(result.id).isEqualTo(1)
        verify(exactly = 1) { repository.save(any()) }
    }
}
```

### Coroutine Testing
```kotlin
@Test
fun `loadUser emits states`() = runTest {
    coEvery { repository.getUser(1) } returns Result.success(user)

    viewModel.state.test {
        viewModel.load(1)
        assertThat(awaitItem().isLoading).isTrue()
        advanceUntilIdle()
        assertThat(awaitItem().user).isEqualTo(user)
    }
}
```

### Compose Testing
```kotlin
@Test
fun `button enabled when fields filled`() {
    composeTestRule.setContent { LoginScreen() }
    composeTestRule.onNodeWithTag("email").performTextInput("a@b.com")
    composeTestRule.onNodeWithTag("password").performTextInput("pass")
    composeTestRule.onNodeWithTag("login_button").assertIsEnabled()
}
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| "no answer found" | Add every { } returns for method |
| Test hangs | Inject TestDispatcher, use advanceUntilIdle() |

## Usage
```
Skill("kotlin-testing")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
