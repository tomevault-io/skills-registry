---
name: container-testing
description: Docker and container testing workflow with Obsidian integration Use when this capability is needed.
metadata:
  author: seqis
---

# Container Testing Protocol Skill

## Overview

Rigorous testing protocol for Docker/Podman container modifications. Container changes have cascading effects - one config change can break multiple apps. This skill enforces comprehensive validation before claiming any container work is "fixed."

## Type

protocol

## When to Invoke

**Trigger keywords:** container, docker, podman, dockerfile, compose, mount, volume, permission, GUI app, display, X11, wayland

**Invoke when:**
- Modifying any container configuration
- Troubleshooting container issues
- Adding packages to containers
- Changing mounts or volumes
- Fixing display/GUI issues in containers
- Working with Obsidian, Chrome, or Brave in containers

## The Container Problem

Docker/Podman changes break critical apps → Regression whack-a-mole → Hours lost fixing same issues

**Why**: Containers are complex ecosystems. One config change cascades everywhere.

## BEFORE Claiming Container "Fixed"

ALL must be YES:
- [ ] **Obsidian launched** AND vault access confirmed?
- [ ] **Chrome/Brave opened** AND can browse websites?
- [ ] **Bash/terminal works** AND commands execute?
- [ ] **File permissions intact** for user directories?
- [ ] **Network connectivity** confirmed (ping, curl)?
- [ ] **Would bet $200** this container actually works for daily use?

## Critical App Test Checklist

### 1. Obsidian Full Test
- [ ] Launch Obsidian GUI from container
- [ ] Open Personal vault: `/path/to/documents/Personal/Obsidian-Notes/Personal-User`
- [ ] Open Work vault: `/path/to/work/P-Drive/_____NOTES_____/Obsidian/WORK`
- [ ] Create test note, verify save works
- [ ] Navigate between vaults without errors

### 2. Browser Full Test
- [ ] Launch Chrome from container GUI
- [ ] Launch Brave from container GUI
- [ ] Navigate to real websites (not just localhost)
- [ ] Verify downloads work to expected directories
- [ ] Test file uploads from container filesystem

### 3. System Integration Test
- [ ] Open terminal, run basic commands (ls, cd, grep)
- [ ] Test file creation/editing in user home directory
- [ ] Verify container can access host filesystem mounts
- [ ] Test permissions for critical directories
- [ ] Confirm environment variables are set correctly

## Container-Specific Red Flags

STOP if you catch yourself thinking:
- "Updated one config" → Likely broke 3 other things
- "Simple mount change" → Permissions nightmare incoming
- "Just added a package" → GUI apps might not start
- "Fixed display" → Network probably broken now

## Regression Prevention Rules

1. **Document current working state** before ANY change
2. **Test ALL critical apps** after EVERY change
3. **Never assume** one fix didn't break something else
4. **Rollback immediately** if any critical app fails
5. **No partial fixes** - everything must work or nothing ships

## Container Test Ritual

| Phase | Action |
|-------|--------|
| **Before** | Document what currently works |
| **Change** | One thing at a time only |
| **After** | Full test suite (all 3 checklists) |
| **Fail** | Rollback immediately + diagnose |
| **Pass** | Document new working state |

## Bottom Line

Container = daily work environment. Broken container = broken productivity.

**TEST EVERYTHING. DOCUMENT EVERYTHING. ROLLBACK FAST.**

*2nd container regression = "incompetent containerization"*

## Integration

This protocol integrates with:
- "Actually Works" Protocol - General verification requirements
- `/fix` command - For systematic debugging of container issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
