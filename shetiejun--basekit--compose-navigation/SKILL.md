---
name: compose-navigation
description: Implement navigation in Jetpack Compose using Navigation Compose. Use when asked to set up navigation, pass arguments between screens, handle deep links, or structure multi-screen apps. Use when this capability is needed.
metadata:
  author: shetiejun
---

# Compose Navigation

## Overview

Implement type-safe navigation in Jetpack Compose applications using the Navigation Compose library. This skill covers NavHost setup, argument passing, deep links, nested graphs, adaptive navigation, and testing.

## Setup

Add the Navigation Compose dependency:

```kotlin
// build.gradle.kts
dependencies {
    implementation("androidx.navigation:navigation-compose:2.8.5")
    
    // For type-safe navigation (recommended)
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.7.3")
}

// Enable serialization plugin
plugins {
    kotlin("plugin.serialization") version "2.0.21"
}
```

---

## Core Concepts

### 1. Define Routes (Type-Safe)

Use `@Serializable` data classes/objects for type-safe routes:

```kotlin
import kotlinx.serialization.Serializable

// Simple screen (no arguments)
@Serializable
object Home

// Screen with required argument
@Serializable
data class Detail(val itemId: String)

// Screen with multiple arguments
@Serializable
data class ProductDetail(
    val productId: String,
    val category: String,
    val fromSearch: Boolean = false
)

@Serializable
data class UserProfile(val userId: Long)

// Screen with optional argument
@Serializable
data class Settings(val section: String? = null)
```

### 2. Create NavHost & Basic Navigation

```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()

    NavHost(
        navController = navController,
        startDestination = Home
    ) {
        composable<Home> {
            HomeScreen(
                onItemClick = { itemId ->
                    navController.navigate(Detail(itemId))
                },
                onSettingsClick = {
                    navController.navigate(Settings())
                }
            )
        }

        composable<Detail> { backStackEntry ->
            val detail: Detail = backStackEntry.toRoute()
            DetailScreen(
                itemId = detail.itemId,
                onBack = { navController.popBackStack() }
            )
        }

        composable<Settings> {
            SettingsScreen(
                onBack = { navController.popBackStack() }
            )
        }
    }
}
```

---

## Navigation Patterns

### Navigation with Arguments

```kotlin
@Composable
fun NavigationWithArgs() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = Home) {
        composable<Home> {
            HomeScreen(
                onProductClick = { productId, category ->
                    navController.navigate(
                        ProductDetail(
                            productId = productId,
                            category = category,
                            fromSearch = false
                        )
                    )
                }
            )
        }

        composable<ProductDetail> { backStackEntry ->
            val args: ProductDetail = backStackEntry.toRoute()
            ProductDetailScreen(
                productId = args.productId,
                category = args.category,
                showBackToSearch = args.fromSearch
            )
        }

        composable<UserProfile> { backStackEntry ->
            val args: UserProfile = backStackEntry.toRoute()
            UserProfileScreen(userId = args.userId)
        }
    }
}
```

### Bottom Navigation

```kotlin
enum class BottomNavDestination(
    val route: Any,
    val icon: ImageVector,
    val label: String
) {
    HOME(Home, Icons.Default.Home, "Home"),
    SEARCH(Search, Icons.Default.Search, "Search"),
    FAVORITES(Favorites, Icons.Default.Favorite, "Favorites"),
    PROFILE(Profile, Icons.Default.Person, "Profile")
}

@Composable
fun MainScreenWithBottomNav() {
    val navController = rememberNavController()
    val navBackStackEntry by navController.currentBackStackEntryAsState()
    val currentDestination = navBackStackEntry?.destination

    Scaffold(
        bottomBar = {
            NavigationBar {
                BottomNavDestination.entries.forEach { destination ->
                    NavigationBarItem(
                        icon = {
                            Icon(destination.icon, contentDescription = destination.label)
                        },
                        label = { Text(destination.label) },
                        selected = currentDestination?.hasRoute(destination.route::class) == true,
                        onClick = {
                            navController.navigate(destination.route) {
                                // Pop up to start destination to avoid building up stack
                                popUpTo(navController.graph.findStartDestination().id) {
                                    saveState = true
                                }
                                // Avoid multiple copies of same destination
                                launchSingleTop = true
                                // Restore state when reselecting
                                restoreState = true
                            }
                        }
                    )
                }
            }
        }
    ) { innerPadding ->
        NavHost(
            navController = navController,
            startDestination = Home,
            modifier = Modifier.padding(innerPadding)
        ) {
            composable<Home> { HomeScreen() }
            composable<Search> { SearchScreen() }
            composable<Favorites> { FavoritesScreen() }
            composable<Profile> { ProfileScreen() }
        }
    }
}
```

### Bottom Nav with Badges

```kotlin
@Composable
fun BottomNavWithBadges(
    cartCount: Int,
    notificationCount: Int
) {
    NavigationBar {
        NavigationBarItem(
            icon = { Icon(Icons.Default.Home, null) },
            label = { Text("Home") },
            selected = true,
            onClick = { }
        )

        NavigationBarItem(
            icon = {
                BadgedBox(
                    badge = {
                        if (cartCount > 0) {
                            Badge { Text("$cartCount") }
                        }
                    }
                ) {
                    Icon(Icons.Default.ShoppingCart, null)
                }
            },
            label = { Text("Cart") },
            selected = false,
            onClick = { }
        )
        // ... more items
    }
}
```

### Navigation Drawer (Modal)

```kotlin
@Composable
fun ModalDrawerNavigation() {
    val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed)
    val scope = rememberCoroutineScope()
    var selectedItem by remember { mutableStateOf(0) }
    
    // ... items setup

    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = {
            ModalDrawerSheet {
                // Header...
                // Navigation items
                items.forEachIndexed { index, item ->
                    NavigationDrawerItem(
                        icon = { Icon(item.icon, contentDescription = null) },
                        label = { Text(item.label) },
                        selected = index == selectedItem,
                        onClick = {
                            selectedItem = index
                            scope.launch { drawerState.close() }
                        },
                        modifier = Modifier.padding(NavigationDrawerItemDefaults.ItemPadding)
                    )
                }
                // Footer...
            }
        }
    ) {
        Scaffold(
            topBar = {
                TopAppBar(
                    title = { Text(items[selectedItem].label) },
                    navigationIcon = {
                        IconButton(onClick = { scope.launch { drawerState.open() } }) {
                            Icon(Icons.Default.Menu, "Open drawer")
                        }
                    }
                )
            }
        ) { padding ->
            Content(modifier = Modifier.padding(padding))
        }
    }
}
```

### Adaptive Navigation (Rail/Drawer)

```kotlin
// Navigation Rail
@Composable
fun NavigationRailLayout() {
    var selectedItem by remember { mutableStateOf(0) }

    Row(modifier = Modifier.fillMaxSize()) {
        NavigationRail(
            header = {
                 FloatingActionButton(onClick = { }) { Icon(Icons.Default.Add, "Create") }
            }
        ) {
            Spacer(Modifier.weight(1f))
            railItems.forEachIndexed { index, item ->
                NavigationRailItem(
                    icon = { Icon(item.icon, null) },
                    label = { Text(item.label) },
                    selected = selectedItem == index,
                    onClick = { selectedItem = index }
                )
            }
            Spacer(Modifier.weight(1f))
        }

        // Main content
        Box(modifier = Modifier.weight(1f).fillMaxHeight()) {
             // Content switching logic
        }
    }
}
```

---

## Deep Linking

### Basic Deep Link Setup

**AndroidManifest.xml**:
```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="myapp" />
    <data android:scheme="https" android:host="myapp.com" />
</intent-filter>
```

**Composable Setup**:
```kotlin
@Composable
fun DeepLinkNavigation() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = Home) {
        composable<Home> { HomeScreen() }

        composable<ProductDetail>(
            deepLinks = listOf(
                navDeepLink<ProductDetail>(basePath = "https://myapp.com/product"),
                navDeepLink<ProductDetail>(basePath = "myapp://product")
            )
        ) { backStackEntry ->
            val args: ProductDetail = backStackEntry.toRoute()
            ProductDetailScreen(productId = args.productId)
        }
    }
}
```

### Handling Intent in Activity

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            AppTheme {
                val navController = rememberNavController()
                // Handle deep link from intent
                LaunchedEffect(Unit) {
                    intent?.data?.let { uri -> navController.handleDeepLink(intent) }
                }
                AppNavigation(navController = navController)
            }
        }
    }

    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        setIntent(intent)
    }
}
```

---

## Nested Navigation

```kotlin
@Composable
fun NestedNavigation() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = MainGraph) {
        // Main graph with bottom navigation
        navigation<MainGraph>(startDestination = Home) {
            composable<Home> {
                HomeScreen(onItemClick = { navController.navigate(Detail(it)) })
            }
            composable<Search> { SearchScreen() }
            composable<Profile> {
                ProfileScreen(onSettingsClick = { navController.navigate(SettingsGraph) })
            }
        }

        // Nested detail graph
        composable<Detail> { backStackEntry ->
            val args: Detail = backStackEntry.toRoute()
            DetailScreen(itemId = args.itemId)
        }

        // Separate settings graph
        navigation<SettingsGraph>(startDestination = SettingsMain) {
            composable<SettingsMain> {
                SettingsScreen(
                    onAccountClick = { navController.navigate(AccountSettings) },
                    onNotificationsClick = { navController.navigate(NotificationSettings) }
                )
            }
            composable<AccountSettings> { AccountSettingsScreen() }
            composable<NotificationSettings> { NotificationSettingsScreen() }
        }
    }
}

// Graph Routes
@Serializable object MainGraph
@Serializable object SettingsGraph
@Serializable object SettingsMain
@Serializable object AccountSettings
@Serializable object NotificationSettings
```

---

## State Management & Animation

### ViewModel Integration

```kotlin
@HiltViewModel
class NavigationViewModel @Inject constructor(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {

    private val _navigationEvents = MutableSharedFlow<NavigationEvent>()
    val navigationEvents = _navigationEvents.asSharedFlow()

    fun navigateToDetail(itemId: String) {
        viewModelScope.launch {
            _navigationEvents.emit(NavigationEvent.NavigateToDetail(itemId))
        }
    }
}

@Composable
fun NavigationHandler(navController: NavHostController, viewModel: NavigationViewModel = hiltViewModel()) {
    LaunchedEffect(Unit) {
        viewModel.navigationEvents.collect { event ->
            when (event) {
                is NavigationEvent.NavigateToDetail -> navController.navigate(Detail(event.itemId))
                NavigationEvent.NavigateBack -> navController.popBackStack()
            }
        }
    }
}
```

### Back Handler

```kotlin
@Composable
fun ScreenWithBackHandler(onBack: () -> Unit) {
    var showExitDialog by remember { mutableStateOf(false) }

    // Intercept back press
    BackHandler { showExitDialog = true }

    if (showExitDialog) {
        AlertDialog(
            onDismissRequest = { showExitDialog = false },
            title = { Text("Exit App?") },
            text = { Text("Are you sure you want to exit?") },
            confirmButton = { TextButton(onClick = onBack) { Text("Exit") } },
            dismissButton = { TextButton(onClick = { showExitDialog = false }) { Text("Cancel") } }
        )
    }
    // Screen content...
}
```

### Navigation Animations

```kotlin
NavHost(
    navController = navController,
    startDestination = Home,
    enterTransition = {
        slideIntoContainer(
            towards = AnimatedContentTransitionScope.SlideDirection.Left,
            animationSpec = tween(300)
        )
    },
    exitTransition = {
        slideOutOfContainer(
            towards = AnimatedContentTransitionScope.SlideDirection.Left,
            animationSpec = tween(300)
        )
    },
    popEnterTransition = {
        slideIntoContainer(
            towards = AnimatedContentTransitionScope.SlideDirection.Right,
            animationSpec = tween(300)
        )
    },
    popExitTransition = {
        slideOutOfContainer(
            towards = AnimatedContentTransitionScope.SlideDirection.Right,
            animationSpec = tween(300)
        )
    }
) {
    // ... destinations
}
```

---

## Testing

### Setup

```kotlin
// build.gradle.kts
androidTestImplementation("androidx.navigation:navigation-testing:2.8.5")
```

### Test Navigation

```kotlin
class NavigationTest {
    @get:Rule
    val composeTestRule = createComposeRule()
    
    private lateinit var navController: TestNavHostController
    
    @Before
    fun setup() {
        composeTestRule.setContent {
            navController = TestNavHostController(LocalContext.current)
            navController.navigatorProvider.addNavigator(ComposeNavigator())
            AppNavHost(navController = navController)
        }
    }
    
    @Test
    fun verifyStartDestination() {
        composeTestRule
            .onNodeWithText("Welcome")
            .assertIsDisplayed()
    }
}
```

---

## Critical Rules

### DO
- Use `@Serializable` routes for type safety
- Pass only IDs/primitives as arguments
- Use `popUpTo` with `launchSingleTop` for bottom navigation
- Extract `NavHost` to a separate composable for testability
- Use `SavedStateHandle.toRoute<T>()` in ViewModels

### DON'T
- Pass complex objects as navigation arguments
- Create `NavController` inside `NavHost`
- Navigate in `LaunchedEffect` without proper keys
- Forget `FLAG_IMMUTABLE` for PendingIntents (Android 12+)
- Use string-based routes (legacy pattern)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shetiejun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
