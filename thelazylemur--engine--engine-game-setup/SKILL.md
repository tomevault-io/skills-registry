---
name: engine-game-setup
description: Set up a new game project using this engine. Use when user wants to create a new game, bootstrap a game project, or understand the game module structure. Covers project layout, main.go, component/system registration, and scene creation. Use when this capability is needed.
metadata:
  author: thelazylemur
---

# Setting Up a New Game Project

This skill guides creation of a complete game project that uses the engine.

## Project Structure

```
mygame/
├── go.mod                    # Module definition
├── cmd/mygame/main.go        # Entry point
├── component/
│   ├── components.go         # ComponentType declarations
│   ├── player.go             # PlayerData struct
│   └── enemy.go              # EnemyData struct
├── system/
│   ├── player.go             # PlayerSystem
│   └── enemy.go              # EnemySystem
├── components.go             # RegisterComponents()
├── systems.go                # RegisterSystems()
└── assets/
    ├── scenes/
    │   └── default.json      # Default scene
    ├── models/               # .obj, .gltf, .glb files
    ├── textures/             # .png, .jpg files
    └── shaders/              # .vs, .fs files
```

## Step 1: Create go.mod

```go
module mygame

go 1.23

require github.com/TheLazyLemur/engine v0.x.x
```

For local development with workspace:

```go
// go.work in parent directory
go 1.23

use (
    ./engine
    ./mygame
)
```

## Step 2: Create main.go

Location: `cmd/mygame/main.go`

```go
package main

import (
    "flag"
    "log"

    engine "github.com/TheLazyLemur/engine/core"

    "mygame"
)

var (
    mode      = flag.String("mode", "editor", "run in game or editor mode")
    assetRoot = flag.String("assets", "./assets", "path to assets directory")
    debug     = flag.Bool("debug", false, "enable debug console in game mode")
)

func main() {
    flag.Parse()
    if *mode != "editor" && *mode != "game" {
        log.Fatal("Please use `game` or `editor` for mode")
    }

    eng := engine.NewEngine(*mode == "editor", *assetRoot, *debug)
    defer eng.Close()

    // Optional: Custom initialization
    eng.InitFunc = func() {
        // Called once before first frame
    }

    // Register game-specific components and systems
    mygame.RegisterComponents(eng)
    mygame.RegisterSystems(eng)

    // Load or create the default scene
    eng.LoadOrCreateScene("scenes/default.json")

    eng.Run()
}
```

## Step 3: Create Component Registry

Location: `component/components.go`

```go
package component

import "github.com/yohamta/donburi"

var (
    Player = donburi.NewComponentType[PlayerData]()
    Enemy  = donburi.NewComponentType[EnemyData]()
    // Add more components here...
)
```

## Step 4: Create Component Data Files

Location: `component/player.go`

```go
package component

import "github.com/TheLazyLemur/engine/math"

type PlayerData struct {
    MoveSpeed   float32 `inspector:"Move Speed,min:0,max:50,step:1"`
    JumpForce   float32 `inspector:"Jump Force,min:0,max:30"`
    IsGrounded  bool    `json:"-"` // Runtime state
}

func NewPlayer() PlayerData {
    return PlayerData{
        MoveSpeed:  10.0,
        JumpForce:  15.0,
        IsGrounded: false,
    }
}
```

## Step 5: Create Registration Functions

Location: `components.go` (root of module)

```go
package mygame

import (
    gameComponent "mygame/component"
    engine "github.com/TheLazyLemur/engine/core"
)

func RegisterComponents(eng *engine.Engine) {
    engine.RegisterComponent(eng, "Player", gameComponent.Player)
    engine.RegisterComponent(eng, "Enemy", gameComponent.Enemy)
    // Add more component registrations...
}
```

Location: `systems.go` (root of module)

```go
package mygame

import (
    gameSystem "mygame/system"
    engine "github.com/TheLazyLemur/engine/core"
)

func RegisterSystems(e *engine.Engine) {
    e.RegisterSystem(&gameSystem.PlayerSystem{})
    e.RegisterSystem(&gameSystem.EnemySystem{})
    // Add more system registrations...
}
```

## Step 6: Create Systems

Location: `system/player.go`

```go
package system

import (
    rl "github.com/gen2brain/raylib-go/raylib"
    "github.com/yohamta/donburi"
    "github.com/yohamta/donburi/filter"

    gameComponent "mygame/component"
    engineComponent "github.com/TheLazyLemur/engine/component"
    engineMath "github.com/TheLazyLemur/engine/math"
    engineScene "github.com/TheLazyLemur/engine/scene"
    sys "github.com/TheLazyLemur/engine/system"
)

type PlayerSystem struct{}

func (s *PlayerSystem) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    query := donburi.NewQuery(filter.Contains(
        engineComponent.Transform,
        gameComponent.Player,
    ))

    for entry := range query.Iter(world) {
        transform := engineComponent.Transform.Get(entry)
        player := gameComponent.Player.Get(entry)

        moveDir := engineMath.Vector3Zero()

        if rl.IsKeyDown(rl.KeyW) { moveDir.Z -= 1 }
        if rl.IsKeyDown(rl.KeyS) { moveDir.Z += 1 }
        if rl.IsKeyDown(rl.KeyA) { moveDir.X -= 1 }
        if rl.IsKeyDown(rl.KeyD) { moveDir.X += 1 }

        if moveDir.LengthSquared() > 0 {
            moveDir = moveDir.Normalize()
            transform.Position = transform.Position.Add(
                moveDir.Scale(player.MoveSpeed * dt),
            )
            transform.MarkDirty()
        }
    }
}

func (s *PlayerSystem) Mode() sys.SystemMode {
    return sys.SystemModeRuntime
}

func (s *PlayerSystem) Name() string {
    return "Player"
}
```

## Step 7: Create Default Scene (Optional)

You can provide a custom scene creator instead of relying on the default:

```go
eng.SetDefaultSceneCreator(func(scn *scene.Scene) {
    // Create player
    player := scn.CreateEntity("Player")
    playerTransform := component.NewTransform()
    playerTransform.Position = math.NewVector3(0, 1, 0)
    scn.SetTransform(player, playerTransform)
    scn.SetRenderMesh(player, component.NewRenderMesh("cube", "blue"))
    scn.AddComponent(player, gameComponent.Player, gameComponent.NewPlayer())

    // Create camera
    camera := scn.CreateEntity("Main Camera")
    cameraTransform := component.NewTransform()
    cameraTransform.Position = math.NewVector3(0, 10, 10)
    scn.SetTransform(camera, cameraTransform)
    scn.SetCamera(camera, component.CameraData{
        Fov:        45.0,
        Near:       0.1,
        Far:        1000.0,
        Projection: component.ProjectionPerspective,
        Active:     true,
        Target:     math.Vector3Zero(),
    })

    // Create ground
    ground := scn.CreateEntity("Ground")
    groundTransform := component.NewTransform()
    groundTransform.Position = math.NewVector3(0, -0.5, 0)
    groundTransform.Scale = math.NewVector3(20, 1, 20)
    scn.SetTransform(ground, groundTransform)
    scn.SetRenderMesh(ground, component.NewRenderMesh("plane", "default"))
})
```

## Step 8: Create Assets Directory

```bash
mkdir -p assets/scenes assets/models assets/textures assets/shaders
```

## Build and Run

```bash
# Build
go build -o bin/mygame ./cmd/mygame

# Run in editor mode
./bin/mygame --mode editor --assets ./assets

# Run in game mode
./bin/mygame --mode game --assets ./assets

# Run with debug console
./bin/mygame --mode game --assets ./assets --debug
```

## Using the Entity Builder API

For procedural entity creation, use the fluent builder:

```go
// Minimal entity
entityId := scene.Spawn("MyEntity").Build()

// Full-featured entity
entityId := scene.Spawn("Enemy").
    At(math.NewVector3(5, 0, 5)).                    // Position
    Rotate(math.QuaternionIdentity()).               // Rotation
    Scale(math.NewVector3(1, 2, 1)).                 // Scale
    Mesh("cube", "red").                             // RenderMesh
    Collider(math.NewVector3(1, 2, 1), math.Vector3Zero()). // OBB collider
    With(gameComponent.Enemy, gameComponent.NewEnemy()).    // Custom component
    Build()
```

## Accessing Engine Registries

```go
// In InitFunc or systems:
meshReg := eng.MeshRegistry()
materialReg := eng.MaterialRegistry()
shaderReg := eng.ShaderRegistry()
audio := eng.Audio()

// List available assets
meshIds := meshReg.ListIds()
materialIds := materialReg.ListIds()
```

## Key Points

1. **Module Structure**: Keep component data in `component/`, systems in `system/`
2. **Registration Order**: Components before systems, systems in dependency order
3. **Asset Loading**: Models/textures auto-load from assets directory
4. **Editor vs Game**: Use `--mode` flag to switch; editor has panels, game is standalone
5. **Scene Persistence**: Scenes auto-save in editor; use LoadOrCreateScene for initial load

## Example Games

See existing implementations:
- `game/` - Full-featured example with shooting, movement, triggers
- `demogame/` - Minimal example with falling blocks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thelazylemur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
