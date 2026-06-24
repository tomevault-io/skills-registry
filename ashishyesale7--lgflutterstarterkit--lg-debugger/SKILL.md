---
name: lg-debugger
description: Diagnoses and fixes issues in LG Flutter apps — SSH connection failures, KML rendering problems, coordinate bugs, Provider errors, and rig communication issues. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# Liquid Galaxy Debugger

## Overview

This skill systematically diagnoses and resolves bugs in Liquid Galaxy Flutter controller applications. It covers the full debugging spectrum from SSH connection problems to KML rendering issues on the rig.

**Announce at start:** "I'm using the lg-debugger skill to diagnose: [Issue Description]."

**GUARDRAIL**: If the student wants to skip understanding the root cause, trigger the **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md). Debugging is learning.

## 🔍 Diagnostic Categories

### 1. SSH Connection Issues
**Symptoms**: App can't connect, timeout errors, "Connection refused."

**Diagnostic Steps:**
1. Verify LG rig is powered on and reachable: `ping <lg-master-ip>`.
2. Verify SSH is running: `ssh lg@<ip>` from terminal.
3. Check credentials: username/password match the rig.
4. Check port: default is 22 for SSH.
5. Check network: phone and rig must be on the same network/subnet.
6. Check `dartssh2` usage: verify `SSHSocket` connection code.

**Common Fixes:**
```dart
// WRONG: Missing timeout
await SSHSocket.connect(host, port);

// RIGHT: Always set a connection timeout
await SSHSocket.connect(host, port, timeout: const Duration(seconds: 10));
```

### 2. KML Not Showing on Google Earth
**Symptoms**: KML sent but nothing appears on the rig screens.

**Diagnostic Steps:**
1. **Validate KML**: Open in Google Earth Pro (desktop) first.
2. **Coordinate order**: KML uses `longitude,latitude` — most bugs are swapped lat/lon.
3. **File path**: Verify writing to `/var/www/html/kml/slave_0.kml` (check slave number).
4. **Clear first**: Old KML might override new. Call `clearKML()` before `sendKML()`.
5. **SSH command**: Verify the `echo '...' > /path` command executes without error.
6. **Google Earth refresh**: GE may need a NetworkLink refresh or manual reload.

**Common Fixes:**
```dart
// WRONG: Lat/Lon swapped
'<coordinates>$latitude,$longitude,0</coordinates>'

// RIGHT: KML is lon,lat,alt
'<coordinates>$longitude,$latitude,0</coordinates>'
```

### 3. Provider / State Management Issues
**Symptoms**: UI doesn't update, "ProviderNotFoundException", stale data.

**Diagnostic Steps:**
1. **Provider registered?** Check `MultiProvider` in `main.dart`.
2. **Watch vs Read**: Use `context.watch<T>()` for reactive rebuilds, `context.read<T>()` for one-time.
3. **notifyListeners()**: Called after every state mutation?
4. **Disposed?**: Service not accessed after `dispose()` called?
5. **Scope**: Provider placed above the widget that needs it in the tree?

### 4. Async/Network Issues
**Symptoms**: App freezes, unhandled exceptions, loading states stuck.

**Diagnostic Steps:**
1. **Try-catch**: Is the async call wrapped?
2. **Timeout**: API or SSH call hanging without timeout?
3. **Loading state**: `_isLoading` set to `false` in `finally` block?
4. **Error propagation**: Exception caught but not displayed to user?

### 5. Build/Compile Errors
**Symptoms**: `flutter analyze` fails, red underlines, missing imports.

**Diagnostic Steps:**
1. `flutter pub get` — dependency resolution.
2. `flutter clean && flutter pub get` — clear build cache.
3. Check `pubspec.yaml` — correct package names and versions.
4. Check imports — relative vs package imports within `lib/`.
5. Check null safety — all `?` and `!` operators used correctly.

## 🛠 Debug Workflow

```
1. Reproduce → Can you trigger the bug consistently?
2. Isolate → Which layer? (UI, Service, SSH, KML, API)
3. Trace → Follow data flow: Widget → Service → SSH → Rig
4. Fix → Apply targeted fix in the correct layer
5. Verify → flutter analyze + flutter test + manual test
6. Document → Add to docs/tech-debt.md if it was a systemic issue
```

## 📋 Quick Reference: LG-Specific Debug Commands

```bash
# Check if LG rig is reachable
ping 192.168.1.100

# SSH into LG master manually
ssh lg@192.168.1.100

# Check if Google Earth is running on the rig
ssh lg@192.168.1.100 "pgrep -f google-earth"

# Read current KML on master
ssh lg@192.168.1.100 "cat /var/www/html/kml/slave_0.kml"

# Read current query (camera control)
ssh lg@192.168.1.100 "cat /tmp/query.txt"

# Clear all KML manually
ssh lg@192.168.1.100 "echo '' > /var/www/html/kml/slave_0.kml"

# Relaunch Google Earth
ssh lg@192.168.1.100 "killall googleearth-bin; sleep 2; /opt/google/earth/pro/googleearth-bin &"
```

## Reference Links

- **Lucia's LG Master App (working reference)**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App
- **dartssh2 docs**: https://pub.dev/packages/dartssh2
- **KML Reference (validate your KML)**: https://developers.google.com/kml/documentation/kmlreference
- **Flutter debugging guide**: https://docs.flutter.dev/testing/debugging
- **LG rig setup/troubleshooting**: https://github.com/LiquidGalaxyLAB/liquid-galaxy/wiki
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## ⛔ Student Interaction Checkpoints

### After Diagnosing — Explain Root Cause

⛔ **STOP and WAIT** — After identifying the bug, ask:
> *"I've found the root cause: [describe the issue]. Before I apply the fix — can you explain in your own words WHY this bug happens? What's the underlying principle being violated?"*

Wait for the student's answer. Evaluate:
- ✅ **Correct**: They understand the root cause (e.g., lat/lon swap, missing `notifyListeners()`, no timeout).
- ⚠️ **Partially correct**: Guide them deeper — ask them to trace the data flow through the affected layers.
- ❌ **Wrong or "just fix it"**: Trigger **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md). Debugging IS learning.

### Before Applying Fix — Predict the Change

⛔ **STOP and WAIT** — Ask:
> *"I'm about to change [file/method]. What do you think the fix looks like? Describe the change before I show you the code."*

Let the student attempt to describe the fix, then show the actual code.

### After Fix — Verify Understanding

⛔ **STOP and WAIT** — After applying and verifying the fix, ask:
> *"How would you prevent this type of bug in the future? Should we add a test for this case?"*

## 📋 Doc Save — Debug Session

For systemic issues, save to `docs/tech-debt.md` (already existing).

Additionally, save debug session context:

**Save to:** `docs/aimentor/YYYY-MM-DD-debug-session.md`

Include:
- Bug description and symptoms
- Root cause analysis
- Fix applied (with file paths)
- Lessons learned
- Prevention strategy (test added? pattern documented?)

## Handoff
After fix → `lg-code-reviewer` for verification that the fix is clean.

## 🔗 Skill Chain

After the fix is applied, verified, and the student understands the root cause, **automatically offer the next stage**:

> *"Bug squashed and verified! You traced the root cause like a pro. Want to resume the pipeline where we left off, or run a code review on the fix? 🐛→✅"*

If student wants to resume → activate `.agent/skills/lg-resume-pipeline/SKILL.md`.
If student wants review → activate `.agent/skills/lg-code-reviewer/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
