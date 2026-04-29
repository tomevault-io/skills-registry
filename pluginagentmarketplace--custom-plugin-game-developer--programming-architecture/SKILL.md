---
name: programming-architecture
description: | Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Game Programming Architecture

## Design Patterns for Games

### 1. State Machine
Best for: Character states, AI, game flow

```csharp
// ✅ Production-Ready State Machine
public abstract class State<T> where T : class
{
    protected T Context { get; private set; }
    public void SetContext(T context) => Context = context;
    public virtual void Enter() { }
    public virtual void Update() { }
    public virtual void Exit() { }
}

public class StateMachine<T> where T : class
{
    private State<T> _current;
    private readonly T _context;

    public StateMachine(T context) => _context = context;

    public void ChangeState(State<T> newState)
    {
        _current?.Exit();
        _current = newState;
        _current.SetContext(_context);
        _current.Enter();
    }

    public void Update() => _current?.Update();
}

// Usage
public class PlayerIdleState : State<Player>
{
    public override void Enter() => Context.Animator.Play("Idle");
    public override void Update()
    {
        if (Context.Input.magnitude > 0.1f)
            Context.StateMachine.ChangeState(new PlayerMoveState());
    }
}
```

### 2. Object Pool
Best for: Bullets, particles, enemies

```csharp
// ✅ Production-Ready Object Pool
public class ObjectPool<T> where T : Component
{
    private readonly Queue<T> _pool = new();
    private readonly T _prefab;
    private readonly Transform _parent;

    public ObjectPool(T prefab, int initialSize, Transform parent = null)
    {
        _prefab = prefab;
        _parent = parent;
        for (int i = 0; i < initialSize; i++)
            _pool.Enqueue(CreateInstance());
    }

    public T Get(Vector3 position)
    {
        var obj = _pool.Count > 0 ? _pool.Dequeue() : CreateInstance();
        obj.transform.position = position;
        obj.gameObject.SetActive(true);
        return obj;
    }

    public void Return(T obj)
    {
        obj.gameObject.SetActive(false);
        _pool.Enqueue(obj);
    }

    private T CreateInstance() => Object.Instantiate(_prefab, _parent);
}
```

### 3. Observer Pattern (Events)
Best for: UI updates, achievements, damage notifications

```csharp
// ✅ Production-Ready Event System
public static class GameEvents
{
    public static event Action<int> OnScoreChanged;
    public static event Action<float> OnHealthChanged;
    public static event Action OnPlayerDied;

    public static void ScoreChanged(int score) => OnScoreChanged?.Invoke(score);
    public static void HealthChanged(float health) => OnHealthChanged?.Invoke(health);
    public static void PlayerDied() => OnPlayerDied?.Invoke();
}

// Subscribe
GameEvents.OnScoreChanged += UpdateScoreUI;

// Always unsubscribe in OnDestroy
private void OnDestroy() => GameEvents.OnScoreChanged -= UpdateScoreUI;
```

### 4. Command Pattern
Best for: Undo/redo, input replay, networking

```csharp
public interface ICommand
{
    void Execute();
    void Undo();
}

public class MoveCommand : ICommand
{
    private readonly Transform _target;
    private readonly Vector3 _direction;
    private Vector3 _previousPosition;

    public MoveCommand(Transform target, Vector3 direction)
    {
        _target = target;
        _direction = direction;
    }

    public void Execute()
    {
        _previousPosition = _target.position;
        _target.position += _direction;
    }

    public void Undo() => _target.position = _previousPosition;
}
```

## Architecture Layers

```
┌─────────────────────────────────────────────────────────────┐
│                    GAME LAYER                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Managers (GameManager, UIManager, AudioManager)      │   │
│  └─────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Systems (Combat, Movement, Inventory, Quest)         │   │
│  └─────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Components (Health, Weapon, CharacterController)     │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                    ENGINE LAYER                              │
│  Physics │ Rendering │ Audio │ Input │ Networking          │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Troubleshooting

```
┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Spaghetti code / tight coupling                     │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Use dependency injection                                  │
│ → Communicate via events, not direct references             │
│ → Follow single responsibility principle                    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Hard to test game systems                           │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Separate logic from MonoBehaviour                         │
│ → Use interfaces for dependencies                           │
│ → Create testable pure functions                            │
└─────────────────────────────────────────────────────────────┘
```

## Best Practices

| Practice | Benefit |
|----------|---------|
| Loose coupling | Systems can change independently |
| Data-driven design | Balance without recompiling |
| Interface abstractions | Easy mocking and testing |
| Single responsibility | Clear, focused classes |

---

**Use this skill**: When architecting systems, improving code structure, or optimizing performance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
