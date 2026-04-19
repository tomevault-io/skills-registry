---
name: shadow-nurture-distribution
description: Active distribution of updates/capabilities from Mother Hive to registered Child Nodes. Use when this capability is needed.
metadata:
  author: uwence
---

# 🍼 Shadow Nurture Protocol

While Evolution pulls improvements *from* children, Nurture pushes improvements *to* children. This ensures that when the Mother Hive advances (e.g., new Tacit Knowledge, improved Skills), all active Legion members receive the upgrade.

## 🛠️ Capabilities

### 1. Distribute Updates
- **Script**: `scripts/distribute.ps1`
- **Inputs**: 
  - `Target` (Optional): specific child path or 'All'.
  - `Component`: 'All', 'Identity', 'Skills', or specific file path relative to `.shadow/`.
- **Actions**:
  1. Read `child_registry.json` to get target nodes.
  2. Copy selected files from Mother Hive to the target Child(ren).
  3. **Safety**: Does NOT overwrite project-specific data (`items.json`, `summary.md`, `mutation_log.json`).

### 2. Logging (Bidirectional Audit Trail)
After each successful nurture operation:
1. **Update Mother's `nurture_history.json`**:
   - Record timestamp, target node, version pushed, and items list.
2. **Update Child's `nurture_log.json`**:
   - Record timestamp, source version, items received, and description.

## 📜 Workflow

### Step 1: Identify Targets
- Read `child_registry.json`.
- Filter by `status: "Active"`.

### Step 2: Copy Files
- For each target node, copy the specified component(s).
- Overwrite existing files (except protected project-specific data).

### Step 3: Write Logs
- Append to `nurture_history.json` (Mother).
- Append to `nurture_log.json` (Child).

### Step 4: Report
- Summarize which nodes received which updates.

## 💻 Usage

```markdown
User: "Push the new 'Shadow Chronos' skill to all child nodes."
Agent: [Executes Skill: Shadow Nurture] --Target "All" --Component "skills/shadow_chronos"

User: "反哺所有子节点"
Agent: [Executes Skill: Shadow Nurture] --Target "All" --Component "All"
```

## 📋 Protected Files (Never Overwrite)
- `memory/life/projects/*` (Project-specific memory)
- `memory/*.md` (Daily notes)
- `mutation_log.json` (Child's own mutations - append only)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uwence) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
