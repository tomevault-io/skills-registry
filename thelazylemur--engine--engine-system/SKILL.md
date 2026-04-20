---
name: engine-system
description: Create new game systems for this engine. Use when the user asks to add gameplay logic, behaviors, AI, movement systems, or any code that processes entities. Systems contain logic; components store data. Use when this capability is needed.
metadata:
  author: thelazylemur
---

# Creating Engine Systems

This skill guides creation of ECS systems that process entities with specific components.

## System Architecture

Systems **query** for entities with required components and **update** them each frame. They implement the `System` interface and optionally `SystemWithRender` for custom rendering.

## System Interface

```go
type System interface {
    Update(world donburi.World, scene *scene.Scene, dt float32)
    Mode() SystemMode
    Name() string
}

// Optional interfaces:
type SystemWithInit interface {
    System
    Init(world donburi.World)
}

type SystemWithRender interface {
    System
    Render(phase RenderPhase, world donburi.World, scene *scene.Scene)
}

type SystemWithCleanup interface {
    System
    Cleanup(world donburi.World)
}
```

## System Modes

| Mode | When it runs |
|------|-------------|
| `SystemModeEdit` | Editor edit mode only |
| `SystemModePlay` | Editor play mode only |
| `SystemModeGame` | Standalone game only |
| `SystemModeAlways` | All modes |
| `SystemModeRuntime` | Play + Game (most common) |

## Basic System Template

Location: `game/system/<name>.go`

```go
package system

import (
    gameComponent "game/component"
    engineComponent "github.com/TheLazyLemur/engine/component"
    engineScene "github.com/TheLazyLemur/engine/scene"
    sys "github.com/TheLazyLemur/engine/system"

    "github.com/yohamta/donburi"
    "github.com/yohamta/donburi/filter"
)

type <Name>System struct{}

func (s *<Name>System) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    query := donburi.NewQuery(
        filter.Contains(
            engineComponent.Transform,
            gameComponent.<ComponentName>,
        ),
    )

    for entry := range query.Iter(world) {
        transform := engineComponent.Transform.Get(entry)
        myComp := gameComponent.<ComponentName>.Get(entry)

        // Your logic here...

        // CRITICAL: Mark dirty after modifying transform
        transform.MarkDirty()
    }
}

func (s *<Name>System) Mode() sys.SystemMode {
    return sys.SystemModeRuntime
}

func (s *<Name>System) Name() string {
    return "<Name>"
}
```

## Register the System

In `game/systems.go`:

```go
func RegisterSystems(e *engine.Engine) {
    // ... existing systems ...
    e.RegisterSystem(&gameSystem.<Name>System{})
}
```

## Common Patterns

### Input Handling System

```go
func (s *PlayerSystem) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    query := donburi.NewQuery(filter.Contains(
        engineComponent.Transform,
        gameComponent.Player,
    ))

    for entry := range query.Iter(world) {
        transform := engineComponent.Transform.Get(entry)
        player := gameComponent.Player.Get(entry)

        moveDir := math.Vector3Zero()

        if rl.IsKeyDown(rl.KeyW) {
            moveDir.Z -= 1
        }
        if rl.IsKeyDown(rl.KeyS) {
            moveDir.Z += 1
        }
        if rl.IsKeyDown(rl.KeyA) {
            moveDir.X -= 1
        }
        if rl.IsKeyDown(rl.KeyD) {
            moveDir.X += 1
        }

        if moveDir.LengthSquared() > 0 {
            moveDir = moveDir.Normalize()
            transform.Position = transform.Position.Add(
                moveDir.Scale(player.MoveSpeed * dt),
            )
            transform.MarkDirty()
        }
    }
}
```

### System with Initialization

```go
type SpawnerSystem struct {
    spawnTimer float32
}

func (s *SpawnerSystem) Init(world donburi.World) {
    s.spawnTimer = 0
}

func (s *SpawnerSystem) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    s.spawnTimer += dt
    if s.spawnTimer >= 2.0 {
        s.spawnTimer = 0
        // Spawn logic...
    }
}
```

### System with Gizmo Rendering

```go
func (s *DebugSystem) Render(phase sys.RenderPhase, world donburi.World, scene *engineScene.Scene) {
    if phase != sys.RenderPhaseGizmos {
        return
    }

    query := donburi.NewQuery(filter.Contains(
        engineComponent.Transform,
        gameComponent.Waypoint,
    ))

    for entry := range query.Iter(world) {
        transform := engineComponent.Transform.Get(entry)
        pos := transform.WorldMatrix.ExtractPosition()

        // Draw debug sphere at waypoint
        rl.DrawSphereWires(pos.ToRaylib(), 0.5, 8, 8, rl.Green)
    }
}
```

### Entity Creation in System

```go
func (s *BulletSpawnerSystem) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    if rl.IsKeyPressed(rl.KeySpace) {
        // Use scene builder API
        bulletId := scene.Spawn("Bullet").
            At(startPos).
            Scale(math.NewVector3(0.1, 0.1, 0.5)).
            Mesh("cube", "yellow").
            With(gameComponent.Bullet, gameComponent.BulletData{
                Speed:     50.0,
                Direction: forward,
                Lifetime:  3.0,
            }).
            Build()
    }
}
```

### Collision Response System

```go
func (s *DamageSystem) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    query := donburi.NewQuery(filter.Contains(
        engineComponent.Transform,
        engineComponent.Colliding,  // Has collision data
        gameComponent.Health,
    ))

    for entry := range query.Iter(world) {
        colliding := engineComponent.Colliding.Get(entry)
        health := gameComponent.Health.Get(entry)

        for _, contact := range colliding.Contacts {
            if contact.State == component.CollisionStateEnter {
                otherEntry := world.Entry(donburi.Entity(contact.OtherEntity))
                if otherEntry.HasComponent(gameComponent.Damage) {
                    dmg := gameComponent.Damage.Get(otherEntry)
                    health.Current -= dmg.Amount
                }
            }
        }
    }
}
```

## Query Patterns

### Multiple Required Components

```go
query := donburi.NewQuery(filter.Contains(
    engineComponent.Transform,
    gameComponent.Movement,
    gameComponent.Health,
))
```

### Optional Components

```go
for entry := range query.Iter(world) {
    // Required
    transform := engineComponent.Transform.Get(entry)

    // Optional check
    if entry.HasComponent(gameComponent.Boost) {
        boost := gameComponent.Boost.Get(entry)
        // Apply boost...
    }
}
```

### Exclude Filter

```go
query := donburi.NewQuery(filter.And(
    filter.Contains(engineComponent.Transform),
    filter.Not(filter.Contains(gameComponent.Dead)),
))
```

## Update Order

Engine runs systems in this order:

1. **Your registered systems** (in registration order)
2. **CollisionSystem** (detects collisions, creates Colliding components)
3. **TransformSystem** (recomputes world matrices from dirty flags)

## Important Rules

1. **Mark Dirty**: Call `transform.MarkDirty()` after modifying position/rotation/scale
2. **Query Once**: Create query at start of Update, iterate once
3. **System Order**: Systems that create entities should register before systems that query them
4. **Collision Timing**: Colliding component data reflects LAST frame's collisions
5. **Render Phases**: Check phase in Render() before drawing

## Render Phases

| Phase | Use Case |
|-------|----------|
| `RenderPhaseShadow` | Custom shadow casters |
| `RenderPhaseGeometry` | Custom 3D rendering |
| `RenderPhaseGizmos` | Debug visualization (lines, spheres) |
| `RenderPhaseOverlay` | 2D text/HUD in viewport |
| `RenderPhaseEditorUI` | ImGui panels (editor only) |

## Example: Complete Rotation System

See existing implementation at:
- System: `game/system/rotation.go`
- Registration: `game/systems.go`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thelazylemur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
