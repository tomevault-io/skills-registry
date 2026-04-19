---
name: spring-tdd-mockito
description: | Use when this capability is needed.
metadata:
  author: mduongvandinh
---

# Spring Boot TDD with Mockito

## TDD Cycle: Red → Green → Refactor

```
┌─────────────────────────────────────────────────────────────┐
│                     TDD WORKFLOW                             │
│                                                              │
│   ┌───────┐      ┌───────┐      ┌──────────┐               │
│   │  RED  │ ───→ │ GREEN │ ───→ │ REFACTOR │ ───┐          │
│   │       │      │       │      │          │    │          │
│   │ Write │      │ Write │      │ Improve  │    │          │
│   │ Test  │      │ Code  │      │ Code     │    │          │
│   └───────┘      └───────┘      └──────────┘    │          │
│       ↑                                          │          │
│       └──────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

## Mockito Annotations

| Annotation | Purpose |
|------------|---------|
| `@Mock` | Create mock object |
| `@InjectMocks` | Inject mocks into class under test |
| `@Spy` | Partial mock - real methods + mock behavior |
| `@Captor` | Capture arguments passed to mock |
| `@MockBean` | Mock bean in Spring context |

## Test Template

```java
@ExtendWith(MockitoExtension.class)
class ServiceTest {

    @Mock
    private Repository repository;

    @InjectMocks
    private Service service;

    @Test
    @DisplayName("Should do something when condition is met")
    void shouldDoSomethingWhenConditionIsMet() {
        // Given (Arrange)
        when(repository.findById(1L)).thenReturn(Optional.of(entity));

        // When (Act)
        var result = service.doSomething(1L);

        // Then (Assert)
        assertThat(result).isNotNull();
        verify(repository).findById(1L);
    }
}
```

## Best Practices

1. **One assertion per test** - Focus on single behavior
2. **Descriptive test names** - Use `@DisplayName`
3. **AAA Pattern** - Arrange, Act, Assert
4. **Test behavior, not implementation**
5. **Fast tests** - Mock external dependencies
6. **Isolated tests** - No shared state

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mduongvandinh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
