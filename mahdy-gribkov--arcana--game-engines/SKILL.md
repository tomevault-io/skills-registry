---
name: game-engines
description: Master game engines - Unity, Unreal Engine, Godot. Engine-specific workflows, systems architecture, and production best practices. Use when this capability is needed.
metadata:
  author: mahdy-gribkov
---

# Game Engines & Frameworks

## Engine Comparison

```
┌─────────────────────────────────────────────────────────────┐
│                    ENGINE COMPARISON                         │
├─────────────────────────────────────────────────────────────┤
│  UNITY (C#):                                                 │
│  ├─ Best for: 2D/3D, Mobile, Indie, VR/AR                  │
│  ├─ Learning: Moderate                                      │
│  ├─ Performance: Good (IL2CPP for native)                  │
│  └─ Market: 70%+ of mobile games                           │
│                                                              │
│  UNREAL (C++/Blueprints):                                    │
│  ├─ Best for: AAA, High-end graphics, Large teams          │
│  ├─ Learning: Steep (C++) / Easy (Blueprints)              │
│  ├─ Performance: Excellent                                  │
│  └─ Market: Major console/PC titles                        │
│                                                              │
│  GODOT (GDScript/C#):                                        │
│  ├─ Best for: 2D games, Learning, Open source              │
│  ├─ Learning: Easy                                          │
│  ├─ Performance: Good for 2D, Improving for 3D             │
│  └─ Market: Growing indie scene                            │
└─────────────────────────────────────────────────────────────┘
```

## Unity Architecture

```
UNITY COMPONENT SYSTEM:
┌─────────────────────────────────────────────────────────────┐
│  GameObject                                                  │
│  ├─ Transform (required)                                    │
│  ├─ Renderer (MeshRenderer, SpriteRenderer)                │
│  ├─ Collider (BoxCollider, CapsuleCollider)                │
│  ├─ Rigidbody (physics simulation)                         │
│  └─ Custom MonoBehaviour scripts                           │
│                                                              │
│  LIFECYCLE:                                                  │
│  Awake() → OnEnable() → Start() → FixedUpdate() →          │
│  Update() → LateUpdate() → OnDisable() → OnDestroy()       │
└─────────────────────────────────────────────────────────────┘
```

```csharp
// ✅ Production-Ready: Unity Component Pattern
public class PlayerController : MonoBehaviour
{
    [Header("Movement Settings")]
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float jumpForce = 10f;

    [Header("Ground Check")]
    [SerializeField] private Transform groundCheck;
    [SerializeField] private float groundRadius = 0.2f;
    [SerializeField] private LayerMask groundLayer;

    private Rigidbody2D _rb;
    private bool _isGrounded;
    private float _horizontalInput;

    // Cache components in Awake
    private void Awake()
    {
        _rb = GetComponent<Rigidbody2D>();
    }

    private void Update()
    {
        // Input in Update (frame-rate independent)
        _horizontalInput = Input.GetAxisRaw("Horizontal");

        if (Input.GetButtonDown("Jump") && _isGrounded)
        {
            Jump();
        }
    }

    private void FixedUpdate()
    {
        // Physics in FixedUpdate (consistent timing)
        CheckGround();
        Move();
    }

    private void CheckGround()
    {
        _isGrounded = Physics2D.OverlapCircle(
            groundCheck.position, groundRadius, groundLayer);
    }

    private void Move()
    {
        _rb.velocity = new Vector2(
            _horizontalInput * moveSpeed,
            _rb.velocity.y);
    }

    private void Jump()
    {
        _rb.velocity = new Vector2(_rb.velocity.x, jumpForce);
    }
}
```

## Unreal Engine Architecture

```
UNREAL ACTOR SYSTEM:
┌─────────────────────────────────────────────────────────────┐
│  AActor (Base class for all game objects)                   │
│  ├─ APawn (Can be possessed by controller)                 │
│  │   └─ ACharacter (Has CharacterMovementComponent)        │
│  ├─ AGameMode (Game rules)                                 │
│  └─ APlayerController (Player input handling)              │
│                                                              │
│  COMPONENTS:                                                 │
│  ├─ USceneComponent (Transform hierarchy)                  │
│  ├─ UStaticMeshComponent (3D model)                        │
│  ├─ UCapsuleComponent (Collision)                          │
│  └─ UCharacterMovementComponent (Movement logic)           │
│                                                              │
│  LIFECYCLE:                                                  │
│  Constructor → BeginPlay() → Tick() → EndPlay()            │
└─────────────────────────────────────────────────────────────┘
```

```cpp
// ✅ Production-Ready: Unreal Character
UCLASS()
class MYGAME_API AMyCharacter : public ACharacter
{
    GENERATED_BODY()

public:
    AMyCharacter();

    virtual void BeginPlay() override;
    virtual void Tick(float DeltaTime) override;
    virtual void SetupPlayerInputComponent(
        UInputComponent* PlayerInputComponent) override;

protected:
    UPROPERTY(EditAnywhere, Category = "Movement")
    float WalkSpeed = 600.0f;

    UPROPERTY(EditAnywhere, Category = "Movement")
    float SprintSpeed = 1200.0f;

    UPROPERTY(EditAnywhere, Category = "Combat")
    float MaxHealth = 100.0f;

private:
    UPROPERTY()
    float CurrentHealth;

    bool bIsSprinting;

    void MoveForward(float Value);
    void MoveRight(float Value);
    void StartSprint();
    void StopSprint();

    UFUNCTION()
    void OnTakeDamage(float Damage, AActor* DamageCauser);
};

// Implementation
void AMyCharacter::BeginPlay()
{
    Super::BeginPlay();
    CurrentHealth = MaxHealth;
    GetCharacterMovement()->MaxWalkSpeed = WalkSpeed;
}

void AMyCharacter::SetupPlayerInputComponent(
    UInputComponent* PlayerInputComponent)
{
    Super::SetupPlayerInputComponent(PlayerInputComponent);

    PlayerInputComponent->BindAxis("MoveForward", this,
        &AMyCharacter::MoveForward);
    PlayerInputComponent->BindAxis("MoveRight", this,
        &AMyCharacter::MoveRight);
    PlayerInputComponent->BindAction("Sprint", IE_Pressed, this,
        &AMyCharacter::StartSprint);
    PlayerInputComponent->BindAction("Sprint", IE_Released, this,
        &AMyCharacter::StopSprint);
}
```

## Godot Architecture

```
GODOT NODE SYSTEM:
┌─────────────────────────────────────────────────────────────┐
│  Node (Base class)                                           │
│  ├─ Node2D (2D game objects)                                │
│  │   ├─ Sprite2D                                            │
│  │   ├─ CharacterBody2D                                     │
│  │   └─ Area2D                                              │
│  ├─ Node3D (3D game objects)                                │
│  │   ├─ MeshInstance3D                                      │
│  │   ├─ CharacterBody3D                                     │
│  │   └─ Area3D                                              │
│  └─ Control (UI elements)                                   │
│                                                              │
│  LIFECYCLE:                                                  │
│  _init() → _ready() → _process() / _physics_process()      │
│                                                              │
│  SIGNALS (Event System):                                     │
│  signal hit(damage)                                         │
│  emit_signal("hit", 10)                                     │
│  connect("hit", target, "_on_hit")                          │
└─────────────────────────────────────────────────────────────┘
```

```gdscript
# ✅ Production-Ready: Godot 4.x Player Controller (GDScript 2.0)
extends CharacterBody2D

class_name Player

# Signals use typed parameters in GDScript 2.0
signal health_changed(new_health: int, max_health: int)
signal died()

@export var move_speed: float = 200.0
@export var jump_force: float = 400.0
@export var max_health: int = 100

# @onready uses $ shorthand for get_node()
@onready var sprite: Sprite2D = $Sprite2D
@onready var animation: AnimationPlayer = $AnimationPlayer
@onready var coyote_timer: Timer = $CoyoteTimer

# get_setting returns ProjectSettings value
var gravity: float = ProjectSettings.get_setting("physics/2d/default_gravity")
var current_health: int
var can_coyote_jump: bool = false

func _ready() -> void:
    current_health = max_health

func _physics_process(delta: float) -> void:
    # Apply gravity
    if not is_on_floor():
        velocity.y += gravity * delta

    # Handle coyote time (jump shortly after leaving edge)
    if is_on_floor():
        can_coyote_jump = true
    elif can_coyote_jump and coyote_timer.is_stopped():
        coyote_timer.start()

    # Jump with Input.is_action_just_pressed (checks input map)
    if Input.is_action_just_pressed("jump"):
        if is_on_floor() or can_coyote_jump:
            velocity.y = -jump_force
            can_coyote_jump = false

    # Horizontal movement - Input.get_axis returns -1 to 1
    var direction := Input.get_axis("move_left", "move_right")
    velocity.x = direction * move_speed

    # Flip sprite based on direction
    if direction != 0:
        sprite.flip_h = direction < 0

    # Update animation state
    _update_animation()

    # move_and_slide() is Godot 4.x API (no parameters needed)
    move_and_slide()

func _update_animation() -> void:
    if not is_on_floor():
        animation.play("jump")
    elif absf(velocity.x) > 10:  # absf for float abs in GDScript 2.0
        animation.play("run")
    else:
        animation.play("idle")

func take_damage(amount: int) -> void:
    current_health = maxi(0, current_health - amount)  # maxi for int max
    health_changed.emit(current_health, max_health)

    if current_health <= 0:
        die()

func die() -> void:
    died.emit()
    queue_free()

func _on_coyote_timer_timeout() -> void:
    can_coyote_jump = false
```

**Godot 4.x API changes:**
- `move_and_slide()` no longer takes parameters (velocity is a property)
- `absf()` and `maxi()`/`maxf()` for typed math functions
- `@export`, `@onready`, `@tool` annotations replace `export`, `onready`
- Typed signals: `signal name(param: Type)`
- `is_on_floor()` replaces `is_on_floor()` (same API, verified working)

## Engine Feature Comparison

```
FEATURE MATRIX:
┌─────────────────────────────────────────────────────────────┐
│  Feature          │ Unity        │ Unreal      │ Godot     │
├───────────────────┼──────────────┼─────────────┼───────────┤
│  2D Support       │ Excellent    │ Basic       │ Excellent │
│  3D Graphics      │ Good         │ Excellent   │ Good      │
│  Physics          │ PhysX/Box2D  │ Chaos/PhysX │ Godot     │
│  Animation        │ Animator     │ AnimGraph   │ AnimTree  │
│  UI System        │ uGUI/UITk    │ UMG/Slate   │ Control   │
│  Networking       │ Netcode/MLAPI│ Built-in    │ ENet/Nakama│
│  Mobile           │ Excellent    │ Good        │ Good      │
│  Console          │ Good         │ Excellent   │ Limited   │
│  VR/AR            │ Excellent    │ Excellent   │ Basic     │
│  Learning Curve   │ Moderate     │ Steep       │ Easy      │
│  License          │ Revenue-based│ 5% royalty  │ MIT Free  │
└───────────────────┴──────────────┴─────────────┴───────────┘
```

## 🔧 Troubleshooting

```
┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Low frame rate in editor                           │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Disable unnecessary editor windows                        │
│ → Reduce scene view quality                                 │
│ → Hide gizmos for complex objects                           │
│ → Build and test outside editor                             │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Physics behaving inconsistently                    │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Use FixedUpdate/_physics_process for physics              │
│ → Check fixed timestep settings                             │
│ → Avoid moving static colliders                             │
│ → Use continuous collision for fast objects                 │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Animations not playing correctly                   │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Check animation state machine transitions                 │
│ → Verify animation clip import settings                     │
│ → Look for conflicting animation layers                     │
│ → Ensure root motion settings match                         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Build size too large                               │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Analyze build report                                      │
│ → Remove unused assets                                      │
│ → Compress textures and audio                               │
│ → Enable code stripping (Unity) / Shipping (Unreal)         │
│ → Split into downloadable content                           │
└─────────────────────────────────────────────────────────────┘
```

## Learning Paths

| Level | Unity | Unreal | Godot |
|-------|-------|--------|-------|
| Beginner (1-2 mo) | Ruby's Adventure | Blueprint Basics | Your First 2D Game |
| Intermediate (2-4 mo) | 3D Platformer | C++ Fundamentals | 3D Game Tutorial |
| Advanced (4-6 mo) | Networking + ECS | Multiplayer Shooter | Multiplayer + Plugins |

---

**Use this skill**: When learning game engines, building games, or optimizing engine performance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mahdy-gribkov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
