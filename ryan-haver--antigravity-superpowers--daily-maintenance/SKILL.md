---
name: daily-maintenance
description: Use at the start of EVERY conversation - performs lightweight daily checks including syncing superpowers skills and workflow templates from GitHub. Check is fast (timestamp comparison) so always run it. Use when this capability is needed.
metadata:
  author: ryan-haver
---

# Daily Maintenance

## Overview

Lightweight daily checks to keep your environment current. **Run this at the start of every conversation** — the timestamp check makes it a no-op most of the time.

Syncs two things:
1. **Superpowers skills** from `obra/superpowers` (Steps 1-3)
2. **Workflow templates** to active project (Step 4)

Both use the same 24-hour freshness check and share one timestamp gate.

## Portable Paths

All steps use these variables. Define them once at the top of any script block:

```powershell
$ANTIGRAVITY_HOME = Join-Path $HOME ".gemini/antigravity"
$SKILLS_DIR       = Join-Path $ANTIGRAVITY_HOME "skills"
$SUPERPOWERS_REPO = Join-Path $SKILLS_DIR "superpowers-repo"
$TEMPLATE_DIR     = Join-Path $SKILLS_DIR "provision-project/templates"
```

> [!IMPORTANT]
> **Never hardcode user paths.** Always derive from `$HOME`. This ensures portability across machines.

## Step 1: Check if update is needed

```powershell
$fetchHead = Join-Path $SUPERPOWERS_REPO ".git/FETCH_HEAD"
if (Test-Path $fetchHead) {
    $hoursSince = (New-TimeSpan -Start (Get-Item $fetchHead).LastWriteTime -End (Get-Date)).TotalHours
    Write-Host "Last sync: $([math]::Round($hoursSince, 1)) hours ago"
} else {
    $hoursSince = 999
    Write-Host "Superpowers repo not found"
}
```

- **Under 24 hours** → Done. Skip ALL remaining steps. Say nothing to the user.
- **Over 24 hours or repo missing** → Continue to Step 2.

## Step 2: Clone or pull superpowers

If repo doesn't exist at `$SUPERPOWERS_REPO`:

```powershell
try {
    git clone https://github.com/obra/superpowers.git $SUPERPOWERS_REPO
    if ($LASTEXITCODE -ne 0) { throw "git clone failed (exit $LASTEXITCODE)" }
} catch {
    Write-Host "⚠️ Superpowers clone failed — $($_.Exception.Message)"
    # Continue to Step 4 (workflow sync) — don't block on this
}
```

If repo exists:

```powershell
try {
    $pullResult = git -C $SUPERPOWERS_REPO pull 2>&1
    if ($LASTEXITCODE -ne 0) { throw "git pull failed (exit $LASTEXITCODE)" }
} catch {
    Write-Host "⚠️ Superpowers pull failed — $($_.Exception.Message)"
    # Continue to Step 4 — stale skills are better than crashing
}
```

- If changes were pulled → Continue to Step 3.
- If "Already up to date." → Skip Step 3, continue to Step 4.
- **If git failed** → Skip Step 3, continue to Step 4. Notify user with a one-liner.

## Step 3: Sync skills (junction-based)

Skills are exposed via Windows junctions pointing into the repo. This means `git pull` in Step 2 instantly updates all skills — no copy step needed.

```powershell
$upstreamManifest = Join-Path $SUPERPOWERS_REPO ".upstream-skills.txt"
$repoSkills = Get-ChildItem -Directory (Join-Path $SUPERPOWERS_REPO "skills") | Select-Object -ExpandProperty Name
$skillChanges = @{ added = @(); removed = @() }

# Create junctions for new skills
foreach ($skillName in $repoSkills) {
    $src  = Join-Path $SUPERPOWERS_REPO "skills\$skillName"
    $dest = Join-Path $SKILLS_DIR $skillName
    if (-not (Test-Path $dest)) {
        cmd /c mklink /J "$dest" "$src" 2>&1 | Out-Null
        if (Test-Path $dest) { $skillChanges.added += $skillName }
    }
    # Existing junctions already point to repo — git pull handles updates
}
```

### Step 3a: Prune orphaned skills

```powershell
# Load previous upstream manifest
$previousSkills = @()
if (Test-Path $upstreamManifest) {
    $previousSkills = Get-Content $upstreamManifest
}

# Find orphans: in old manifest but not in current repo
$orphans = $previousSkills | Where-Object { $_ -and ($repoSkills -notcontains $_) }
foreach ($orphan in $orphans) {
    $orphanPath = Join-Path $SKILLS_DIR $orphan
    if (Test-Path $orphanPath) {
        # Only remove if it's a junction (don't delete custom skills)
        $item = Get-Item $orphanPath
        if ($item.Attributes -band [IO.FileAttributes]::ReparsePoint) {
            cmd /c rmdir "$orphanPath" 2>&1 | Out-Null
            $skillChanges.removed += $orphan
        }
    }
}

# Save current manifest for next run
$repoSkills | Set-Content $upstreamManifest
```

## Step 4: Sync templates to current project

Only runs if the current workspace has `.agent/workflows/`:

```powershell
$projectWf = Join-Path $PWD ".agent/workflows"
if (Test-Path $projectWf) {
    $updated = @()
    $skipped = @()
    $removed = @()

    # Copy new/changed templates
    Get-ChildItem $TEMPLATE_DIR -Filter "*.md" | ForEach-Object {
        $dest = Join-Path $projectWf $_.Name
        if (Test-Path $dest) {
            $firstLine = Get-Content $dest -TotalCount 1
            if ($firstLine -match "^\s*#\s*LOCKED") { $skipped += $_.Name; return }
            $srcHash = (Get-FileHash $_.FullName -Algorithm MD5).Hash
            $destHash = (Get-FileHash $dest -Algorithm MD5).Hash
            if ($srcHash -eq $destHash) { return }
        }
        Copy-Item $_.FullName $dest -Force
        $updated += $_.Name
    }

    # Remove project workflows that were pruned from templates
    Get-ChildItem $projectWf -Filter "*.md" | ForEach-Object {
        $inTemplates = Test-Path (Join-Path $TEMPLATE_DIR $_.Name)
        if (-not $inTemplates) {
            $firstLine = Get-Content $_.FullName -TotalCount 1
            if ($firstLine -match "^\s*#\s*LOCKED") { return }
            Remove-Item $_.FullName -Force
            $removed += $_.Name
        }
    }

    if ($updated.Count -gt 0 -or $removed.Count -gt 0) {
        Write-Host "Project workflows: $($updated.Count) synced, $($removed.Count) pruned"
    }

    # Ensure .gemini/workflows junction exists for VS Code Gemini Code Assist
    $geminiWf = Join-Path $PWD ".gemini/workflows"
    if (!(Test-Path $geminiWf)) {
        $geminiDir = Join-Path $PWD ".gemini"
        if (!(Test-Path $geminiDir)) { New-Item -ItemType Directory -Path $geminiDir -Force | Out-Null }
        New-Item -ItemType Junction -Path $geminiWf -Target $projectWf | Out-Null
        Write-Host "Created .gemini/workflows junction for VS Code discovery"
    }
}
```

## Step 5: Summary notification

Only notify the user if **anything** actually changed. Combine all changes into one concise message:

```powershell
$parts = @()
if ($skillChanges.added.Count -gt 0)   { $parts += "$($skillChanges.added.Count) skills added" }
if ($skillChanges.removed.Count -gt 0) { $parts += "$($skillChanges.removed.Count) orphaned skills pruned" }
if ($updated.Count -gt 0)              { $parts += "$($updated.Count) project workflows synced" }
if ($removed.Count -gt 0)              { $parts += "$($removed.Count) project workflows pruned" }
if ($skipped.Count -gt 0)              { $parts += "$($skipped.Count) LOCKED (skipped)" }

if ($parts.Count -gt 0) {
    Write-Host "🔄 Daily sync: $($parts -join ', ')."
} 
# If nothing changed, say nothing
```

Example output:
```
🔄 Daily sync: 2 skills added, 3 project workflows synced.
```

## Key Rules

- **Always run Step 1** at conversation start — it's one command
- **Silent when fresh** — don't mention anything if no update was needed
- **Only notify on actual changes** — user shouldn't know this runs unless something updated
- **Never overwrite LOCKED files** — if a project workflow starts with `# LOCKED`, skip it
- **All steps share the same 24-hour gate** — one timestamp controls all syncs
- **Fail gracefully** — git failures log a warning and continue; never block the conversation
- **Prune orphans** — removed/renamed upstream skills are cleaned up automatically
- **No hardcoded paths** — all paths derive from `$HOME`; portable across machines

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-haver) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
