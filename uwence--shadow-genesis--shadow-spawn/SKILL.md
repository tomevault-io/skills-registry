---
name: shadow-spawn-genesis
description: Create new Shadow instances (Child Nodes) and register them directly in the Mother Hive's consciousness. Use when this capability is needed.
metadata:
  author: uwence
---

# 🐣 Shadow Spawn Protocol

This skill replaces the manual `genesis.ps1` execution. It spawns a new "Shadow" agent in a target directory and registers its existence for future evolution/nurturing.

## 🛠️ Capabilities

### 1. Spawn Child
- **Script**: `scripts/spawn.ps1`
- **Inputs**: 
  - `ProjectName`: Name of the new project.
  - `TargetDirectory`: Where to create it.
- **Actions**:
  - Clones the `.shadow` template structure.
  - Customizes `IDENTITY.md` and `USER.md`.
  - **Registers** the child in `.shadow/memory/life/resources/system/child_registry.json`.

## 💻 Usage

```markdown
User: "Spawn a new project named 'Opendoc-Tools' in D:\Projects\OpenDoc"
Agent: [Executes Skill: Shadow Spawn] --ProjectName "Opendoc-Tools" --TargetDirectory "D:\Projects\OpenDoc"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uwence) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
