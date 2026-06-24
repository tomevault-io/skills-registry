# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Bear_Persona is a 2D action game built with Unity (URP 2D Pipeline) featuring a "possession" mechanic where the player switches control between bear units. Units not under player control run AI via a finite state machine.

## Code Style

- **All comments, documentation, and string literals must be in Simplified Chinese (简体中文).**
- Private fields: `_camelCase` (e.g., `_moveAction`)
- Public properties/methods: `PascalCase`
- Interfaces: `IPascalCase` (e.g., `IState`, `ISwitchable`)
- Use `[SerializeField]`, `[Header]`, `[Tooltip]` attributes to organize Inspector fields
- Reference implementation: `Assets/Script/Ability/ChargeAttack.cs`

## Architecture

### Core Systems

- **PlayerController** (Singleton) — Central manager for input, possession switching, and death-possession logic. Uses `InputActionReference` from Unity's Input System.
- **TimeManager** (Singleton) — Controls time scaling for bullet-time during possession mode. Fires `OnDeathPossessionTimeout` when the death-possession window expires.
- **BearUnit** — Main unit component. Holds the `StateMachine`, reads config from `UnitData` ScriptableObject, and switches Tag/Layer between "Player" and "Enemy" based on control status.

### FSM (`Assets/Script/FSM/`)

`IState` interface with `Enter()`, `Execute()`, `Exit()`. `StateMachine` manages transitions.

States:
- **ControlledState** — Active when player possesses the unit; routes input to movement/attack
- **IdleState / PatrolState / ChaseState / AttackState** — AI behaviors for non-possessed units

### Combat (`Assets/Script/Ability/`)

- **ChargeAttack** — Hold-to-charge attack. Supports Rectangle (`Physics2D.OverlapBox`) and Circle AOE (`Physics2D.OverlapCircle`). Friendly fire disabled via Tag comparison.
- **Indicators** — `IAttackIndicator` interface with `RectIndicator` and `CircleIndicator` implementations. Visual layer is separated from damage logic.

### Data-Driven Config

Unit stats are defined in `UnitData` ScriptableObjects (`Assets/Data/`), created via menu `BearPersona/UnitData`. This includes move speed, aggro range, attack shape type, charge time, and attack dimensions.

### Possession Flow

1. `PlayerController.TogglePossessionMode()` → slows time via `TimeManager`
2. `RangeCircle` indicator appears around current unit
3. Player clicks a target within range → `SwitchControlTo()` releases old unit, sets new unit as controlled
4. On death: `OnControlledUnitDeath()` → `StartDeathPossession()` gives a timed window to find a new host, otherwise triggers game over

## Project Layout

- `Assets/Script/` — All C# source code
- `Assets/Data/` — ScriptableObject assets (unit configurations)
- `Assets/Prefab/` — Reusable prefabs
- `Assets/Input/` — Input System action maps
- `Assets/Scenes/` — Scene files
- `Doc/` — Feature specs (e.g., `PossessionMechanic.md`, `DebugFeatures.md`)

## Build

Standard Unity project. Open in Unity Editor and build via `File > Build Settings`. No custom build scripts.

## Dependencies

- Unity Input System
- Universal Render Pipeline (URP) 2D
- 2D Animation
- TextMeshPro

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/superbear1945)
> Context snippets also available to append to your CLAUDE.md, GEMINI.md, and copilot-instructions.md — [download at TomeVault](https://tomevault.io/claim/superbear1945)
<!-- tomevault:4.0:agents_md:2026-04-08 -->
