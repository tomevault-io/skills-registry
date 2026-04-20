---
name: game-system
description: Create new game systems for this project. Use when adding gameplay logic, AI, movement, or any entity processing. Systems contain logic; components store data. Use when this capability is needed.
metadata:
  author: thelazylemur
---

# Creating Game Systems

## Quick Template

`system/<name>.go`:

```go
package system

import (
    "github.com/yohamta/donburi"
    "github.com/yohamta/donburi/filter"

    gameComponent "<module>/component"
    engineComponent "github.com/TheLazyLemur/engine/engine/component"
    engineScene "github.com/TheLazyLemur/engine/engine/scene"
    sys "github.com/TheLazyLemur/engine/engine/system"
)

type <Name>System struct{}

func (s *<Name>System) Update(world donburi.World, scene *engineScene.Scene, dt float32) {
    query := donburi.NewQuery(filter.Contains(
        engineComponent.Transform,
        gameComponent.<Required>,
    ))

    for entry := range query.Iter(world) {
        transform := engineComponent.Transform.Get(entry)
        data := gameComponent.<Required>.Get(entry)

        // Logic here...

        transform.MarkDirty() // If position changed
    }
}

func (s *<Name>System) Mode() sys.SystemMode { return sys.SystemModeRuntime }
func (s *<Name>System) Name() string { return "<Name>" }
```

Register in `systems.go`:

```go
e.RegisterSystem(&gameSystem.<Name>System{})
```

## System Modes

| Mode | When |
|------|------|
| `SystemModeRuntime` | Play + Game (most common) |
| `SystemModeEdit` | Editor only |
| `SystemModeGame` | Standalone only |
| `SystemModeAlways` | All modes |

## Patterns

**Input**: `rl.IsKeyDown(rl.KeyW)`
**Render**: Implement `SystemWithRender`, check `phase`
**Init**: Implement `SystemWithInit` for one-time setup
**Query filter**: `filter.And(filter.Contains(...), filter.Not(...))`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thelazylemur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
