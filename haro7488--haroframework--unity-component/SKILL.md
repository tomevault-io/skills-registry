---
name: unity-component
description: Create well-structured Unity MonoBehaviour components following Unity 6 best practices with proper lifecycle methods, serialization, and namespace organization. Use this skill when the user needs to create gameplay scripts, controllers, managers, or any MonoBehaviour-based component. Use when this capability is needed.
metadata:
  author: haro7488
---



<!-- Navigation -->
**🏠 [HaroFramework Project](../../MASTER_INDEX.md)** | **📂 [Skill](./)** | **⬆️ [Skill](./)**

---
# Unity Component Builder

Expert skill for creating production-ready Unity MonoBehaviour components.

## When to Use This Skill

Activate this skill when the user requests:
- Creating a new MonoBehaviour script
- Building gameplay controllers (player, enemy, camera)
- Implementing game managers or systems
- Creating UI controllers
- Building component-based game mechanics

## Component Structure Standards

### Namespace Organization
```csharp
namespace HaroFramework.[Category]
{
    // Component code
}
```

Categories: Core, Player, AI, UI, Audio, Gameplay, Systems

### Component Template
```csharp
using UnityEngine;

namespace HaroFramework.[Category]
{
    /// <summary>
    /// [Component purpose and usage]
    /// </summary>
    [RequireComponent(typeof(ComponentType))] // If applicable
    public class ComponentName : MonoBehaviour
    {
        #region Inspector Fields

        [Header("Configuration")]
        [SerializeField] private Type _fieldName;
        [Tooltip("Description of field")]
        [SerializeField] private Type _fieldName;

        #endregion

        #region Private Fields

        private Type _cachedComponent;

        #endregion

        #region Properties

        public Type PropertyName { get; private set; }

        #endregion

        #region Unity Lifecycle

        private void Awake()
        {
            // Cache components
            // Initialize references
        }

        private void OnEnable()
        {
            // Subscribe to events
        }

        private void Start()
        {
            // Initialize state
        }

        private void Update()
        {
            // Per-frame logic (use sparingly)
        }

        private void FixedUpdate()
        {
            // Physics updates
        }

        private void LateUpdate()
        {
            // Camera or post-frame logic
        }

        private void OnDisable()
        {
            // Unsubscribe from events
        }

        private void OnDestroy()
        {
            // Cleanup
        }

        #endregion

        #region Public Methods

        /// <summary>
        /// [Method description]
        /// </summary>
        public void MethodName()
        {

        }

        #endregion

        #region Private Methods

        private void PrivateMethod()
        {

        }

        #endregion

        #region Editor

        #if UNITY_EDITOR
        private void OnValidate()
        {
            // Validate inspector values
        }

        private void OnDrawGizmos()
        {
            // Debug visualization
        }
        #endif

        #endregion
    }
}
```

## Best Practices

### Performance
- Cache component references in `Awake()`
- Avoid `GetComponent<>()` in `Update()`
- Use `FixedUpdate()` for physics
- Consider object pooling for frequently instantiated objects

### Unity 6 Features
- Use `FindFirstObjectByType<T>()` instead of deprecated `FindObjectOfType<T>()`
- Leverage new MonoBehaviour spelling (not Behavior)
- Use modern Input System (InputAction, InputActionReference)

### Serialization
- Use `[SerializeField]` for private fields that need inspector visibility
- Add `[Header("Section")]` for organization
- Include `[Tooltip("Description")]` for clarity
- Use `[Range(min, max)]` for numeric constraints

### Code Organization
- Group related fields with `#region`
- Keep Unity lifecycle methods in standard order
- Separate public API from private implementation
- Add XML documentation for public methods

### Event Management
- Subscribe in `OnEnable()`, unsubscribe in `OnDisable()`
- Use UnityEvents for designer-configurable callbacks
- Consider C# events for code-only communication

## Common Patterns

### Singleton (if needed)
```csharp
public class Manager : MonoBehaviour
{
    public static Manager Instance { get; private set; }

    private void Awake()
    {
        if (Instance != null && Instance != this)
        {
            Destroy(gameObject);
            return;
        }

        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
}
```

### Component Communication
```csharp
// Option 1: Direct reference
[SerializeField] private OtherComponent _other;

// Option 2: GetComponent
private OtherComponent _other;
private void Awake() => _other = GetComponent<OtherComponent>();

// Option 3: Events
public event System.Action OnEventTriggered;
```

## Questions to Ask

Before creating a component, clarify:
1. What is the component's primary responsibility?
2. What other components does it depend on?
3. Does it need to persist across scenes?
4. What inspector configuration is needed?
5. Are there performance considerations?

## Output Format

1. Create the .cs file in appropriate directory (Assets/Scripts/[Category]/)
2. Include complete, production-ready code
3. Add inline comments for complex logic
4. Suggest related components or next steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haro7488) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
