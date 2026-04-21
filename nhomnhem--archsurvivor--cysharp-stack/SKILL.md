---
name: cysharp-stack
description: High-performance Unity development using Cysharp tools (UniTask, R3, ZLinq, MessagePipe). Use when this capability is needed.
metadata:
  author: nhomnhem
---

# Cysharp Standard Stack - Optimization & Architecture

> **MANDATORY** for ArchSurvivor performance-critical components. Use these tools to achieve zero-allocation, reactive, and decoupled game logic.

---

## 🚀 The Core Four

| Tool | Purpose | Key Benefit |
| :--- | :--- | :--- |
| **UniTask** | Efficient Async/Await | Zero-allocation tasks, better than Coroutines. |
| **R3** | Reactive Extensions | Modern, faster Rx for state and UI binding. |
| **ZLinq** | Zero-Alloc LINQ | Performance of for-loops with LINQ syntax. |
| **MessagePipe** | Global Pub/Sub | Decouples systems (Event Bus). |
| **VContainer** | DI Framework | Core structural backbone for decoupling. |
| **Sisus.Init** | Data Injection | High-performance data injection for MonoBehaviours. |

---

## 1. ZLinq - Performance Rule

**NEVER** use standard `System.Linq` in `Update`, `LateUpdate`, or high-frequency loops.

| ❌ Standard LINQ (Allocates) | ✅ ZLinq (Zero-Alloc) |
| :--- | :--- |
| `list.FirstOrDefault(x => x == null)` | `list.AsValueEnumerable().FirstOrDefault(x => x == null)` |
| `targets.Where(t => t.IsAlive)` | `targets.AsValueEnumerable().Where(t => t.IsAlive)` |

**Rule:** Always add `using ZLinq;` and use `.AsValueEnumerable()` for list/dictionary/collection processing in hot paths.

---

## 2. R3 vs MessagePipe - Architecture Rule

| Scenario | Use **R3** | Use **MessagePipe** |
| :--- | :--- | :--- |
| **Scope** | Local / Component | Global / Cross-Module |
| **State** | Yes (`ReactiveProperty`) | No (One-time events) |
| **Dependency** | Direct (need reference) | Loose (don't need reference) |
| **Example** | `PlayerHP.Value` -> UI | `EnemyKilled` -> Quest System |

---

## 3. UniTask - Async Rule

- Use `UniTaskVoid` for "fire and forget" methods (e.g., button clicks).
- Use `CancellationToken` from `this.GetCancellationTokenOnDestroy()` to prevent memory leaks and orphaned tasks.
- Always use `await UniTask.Yield(PlayerLoopTiming.Update)` instead of `yield return null`.

---

## 4. MessagePipe Implementation

**Registration (VContainer):**

```csharp
var options = builder.RegisterMessagePipe();
builder.RegisterMessageBroker<MyMessage>(options);
```

**Publishing:**

```csharp
[Inject] IPublisher<MyMessage> publisher;
publisher.Publish(new MyMessage());
```

**Subscribing:**

```csharp
[Inject] ISubscriber<MyMessage> subscriber;
subscriber.Subscribe(msg => { /* Logic */ }).AddTo(disposables); // Link to R3 CancellationToken/Disposable
```

---

## 5. Senior VContainer Patterns (P1)

### A. Register Collections (Factory Pattern)

Auto-resolve multiple implementations via `IEnumerable<T>`.

```csharp
builder.Register<IAbility, Fireball>(Lifetime.Singleton);
builder.Register<IAbility, FrostBolt>(Lifetime.Singleton);

// Resolution:
public AbilityManager(IEnumerable<IAbility> allAbilities) { /* ... */ }
```

### B. Register ScriptableObject Settings

Register specific data classes from a main settings asset.

```csharp
[SerializeField] GameSettings _settings;
builder.RegisterInstance(_settings.CombatSettings);
builder.RegisterInstance(_settings.AudioSettings);
```

### C. Register with Keys (Variant Strategy)

Use enums to distinguish implementations when a collection isn't enough.

```csharp
builder.Register<IWeapon, Sword>(Lifetime.Singleton).Keyed(WeaponType.Primary);
builder.Register<IWeapon, Bow>(Lifetime.Singleton).Keyed(WeaponType.Secondary);

// Resolution:
public WeaponSystem([Key(WeaponType.Primary)] IWeapon primary) { /* ... */ }
```

### D. Lifetime Callbacks

Hook into container lifecycle for manual initialization.

```csharp
builder.RegisterBuildCallback(container => {
    var service = container.Resolve<InitializableService>();
    service.Boot();
});
```

### E. Advanced Factories (Func<T>)

Register factories with dependencies handled by the container.

```csharp
// Factory with runtime parameter + container dependency
builder.RegisterFactory<int, Foo>(container => {
    var dependency = container.Resolve<Dependency>();
    return x => new Foo(x, dependency);
}, Lifetime.Scoped);
```

### F. MonoBehaviour & Prefab Patterns

Register existing components or handle dynamic instantiation.

```csharp
// 1. Existing Component
builder.RegisterComponent(myInstance);

// 2. Component in Hierarchy (Scene-based)
builder.RegisterComponentInHierarchy<MySystem>();

// 3. New Prefab Resolve (Auto-Instantiate)
builder.RegisterComponentInNewPrefab(prefab, Lifetime.Scoped).UnderTransform(parent);
```

### G. Code-First Child Scoping (Senior Pattern)

Dynamically create sub-systems (e.g., Level-specific systems) from code.

```csharp
// Inside a service:
using (var childScope = _lifetimeScope.CreateChild(builder => {
    builder.RegisterInstance(levelData);
})) {
    var levelManager = childScope.Container.Resolve<LevelManager>();
}
```

---

## 6. Sisus.Init Patterns (Hybrid DI Strategy)

**Standard:** Use a hybrid approach where VContainer handles *Systems* and Sisus handles *Data*.

| Scenario | Use **VContainer** | Use **Sisus.Init (InitArgs)** |
| :--- | :--- | :--- |
| **Object Type** | Services, Managers, Logic Classes | MonoBehaviours (Prefabs/Scene) |
| **Injected Content** | Interfaces (`IService`), Global State | POCO Data (`CharacterData`), Configs |
| **Resolution** | Constructor Injection | `IInitializable<T>` or `InitArgs.Set` |

### A. The "Factory-Init" Pattern

When a Factory spawns a prefab, it sets the data via Sisus just before instantiation.

```csharp
private void SetupInjection(CharacterRuntimeData data) {
    // 1. Resolve global dependencies from VContainer
    var input = _objectResolver.Resolve<IInputReader>();
    
    // 2. Set them for the next instantiated object via Sisus
    InitArgs.Set<HeroController, CharacterRuntimeData, IInputReader>(data, input);
    
    // 3. VContainer then handles [Inject] fields on the instance
    var go = Object.Instantiate(prefab);
    _objectResolver.InjectGameObject(go);
}
```

### B. Why use Sisus.Init?

- **Performance**: Faster than recursive reflection for deep hierarchies.
- **Type Safety**: Enforces initialization arguments at compile time.
- **Scene Integration**: Allows MonoBehaviours in the scene to be initialized before `Awake`.

---

## 7. Scope-Aware Architecture (Memory Efficiency)

**Rule:** Systems must be registered in the **lowest possible scope** to minimize memory footprint and ensure automatic cleanup.

### A. Lifecycle Boundaries

| Scope | Content | Lifetime |
| :--- | :--- | :--- |
| **Project** | SaveData, Master Data, Global Audio, Settings | Application Run |
| **Scene (Game)** | Spawners, Radar, Input, Combat Juice, Level Logic | Single Level/Run |
| **Object (Hero)** | FSM, Local Sensors, Animation State | Instance Life |

### B. The "Cleanup" Protocol

1. **Self-Contained Scene**: All gameplay-specific services must be in `GameLifetimeScope`.
2. **Automatic Disposal**: Use `builder.Register<T>(Lifetime.Singleton)` inside a Scene Scope. VContainer will automatically call `Dispose()` when the scene is unloaded.
3. **Avoid Project Bloat**: Never register "Gameplay-only" services (e.g., `EnemySpawner`) in `ProjectLifetimeScope`. This prevents memory leaks and "stale state" from the previous run.

---

## 🎮 Survivor Specialty: Scaling to 1000+ Enemies

When managing the Radar for a Survivor game:

1. **Loop**: Use `ZLinq` to filter targets.
2. **Events**: Use `MessagePipe` to register new targets without polling.
3. **Pooling**: Use `MessagePipe` to notify systems when an object is returned to pool.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nhomnhem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
