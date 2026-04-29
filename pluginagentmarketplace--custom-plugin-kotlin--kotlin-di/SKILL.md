---
name: kotlin-di
description: Dependency Injection - Hilt, Koin, scopes, testing Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Kotlin DI Skill

Dependency Injection with Hilt and Koin.

## Topics Covered

### Hilt for Android
```kotlin
@HiltAndroidApp
class App : Application()

@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    @Provides @Singleton
    fun provideDatabase(@ApplicationContext context: Context) =
        Room.databaseBuilder(context, AppDatabase::class.java, "app.db").build()

    @Provides
    fun provideUserDao(db: AppDatabase) = db.userDao()
}

@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel()
```

### Koin for Multiplatform
```kotlin
val appModule = module {
    single { HttpClient(getEngine()) }
    single { UserRepository(get()) }
    viewModel { UserViewModel(get()) }
}

// Start Koin
startKoin {
    modules(appModule)
}

// Inject
val repository: UserRepository by inject()
```

### Testing with DI
```kotlin
@HiltAndroidTest
class UserViewModelTest {
    @get:Rule val hiltRule = HiltAndroidRule(this)

    @BindValue @JvmField
    val repository: UserRepository = mockk()

    @Inject lateinit var viewModel: UserViewModel

    @Before fun setup() { hiltRule.inject() }
}
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| "No binding for..." | Add @Provides or @Binds |
| ViewModel not injected | Use hiltViewModel() in Compose |

## Usage
```
Skill("kotlin-di")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
