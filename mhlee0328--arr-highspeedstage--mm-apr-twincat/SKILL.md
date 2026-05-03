---
name: mm-apr-twincat
description: | Use when this capability is needed.
metadata:
  author: mhlee0328
---

# TwinCAT 3 Development Guide for APR System

> **Version**: APR-2026.01 | **TwinCAT**: 3.1 Build 4024.65

Development environment setup and best practices for HIWIN LMSA13L control.

## Overview

This skill covers TwinCAT 3 development for the APR dual-axis linear motor system, including version selection, project setup, extension libraries, and ST programming patterns.

## Version Strategy

| Environment | Version | Rationale |
|-------------|---------|-----------|
| Development (Laptop) | 4026.19 | Latest features, VS2022, TwinSAFE editor |
| Production (C6030) | **4024.65** | LTS stable, HIWIN verified (MD38UC01) |

### Windows 11 Compatibility

For local simulation on Windows 11:
- **Use 4024.65 or earlier** (4024.64, 4024.47)
- Versions 4024.66+ have poor Win11 local support
- Install with Administrator + Complete option

### Installation Notes

```
✓ TC31-FULL-Setup.3.1.4024.65.exe (Administrator + Complete)
✗ Do NOT install XAR/ADS/RM separately after Full installation
```

After installation, run as Administrator:
```
"C:\TwinCAT\3.1\System\win8settick.bat"
```

## Project Setup

### Recommended Project Type

In Visual Studio: **TwinCAT XAE Project (XML format)**

### Naming Conventions

| Type | Prefix | Example |
|------|--------|---------|
| Global Variables | `g_` | `g_bSystemReady` |
| Function Blocks | `FB_` | `FB_HomingAxis1` |
| Structures | `ST_` | `ST_AxisState` |
| Safety Variables | `saf_` | `saf_bEStop` |
| Programs | `PRG_` | `PRG_Main` |
| Enumerations | `E_` | `E_HomeScanState` |

### NC Axis Configuration

1. Add NC Configuration → Axes → Axis 1
2. Set engineering units: **mm / mm/s / mm/s²**
3. Configure software limits:
   - Calibration mode: `[-50, 4250]` mm (allow overshoot)
   - Production mode: `[0, 4200]` mm or `[10, 4190]` mm
4. Link to E1 drive PDO objects

## Extension Libraries

### SPT-Libraries (Beckhoff USA Community)

GitHub: `Beckhoff-USA-Community/SPT-Libraries`

Features:
- Utility function blocks
- State machine templates
- Diagnostic tools

### Loupe Team Frameworks

GitHub: `loupeteam/`

Relevant repositories:
- `StarterAsProject` - Project template
- `CSVFileLib` - CSV file operations
- `Omniverse_Beckhoff_Bridge` - Isaac Sim integration

### Installation

Use LPM (Loupe Package Manager) or manual import:
1. Download library files
2. TwinCAT → PLC → References → Add Library
3. Install to Library Repository if needed

## State Machine Pattern (ST)

### Recommended Structure

```iecst
TYPE E_MachineState :
(
    ST_IDLE,
    ST_INIT,
    ST_RUNNING,
    ST_ERROR,
    ST_STOPPING
);
END_TYPE

FUNCTION_BLOCK FB_StateMachine
VAR
    eState      : E_MachineState := ST_IDLE;
    eStatePrev  : E_MachineState;
    bStateEntry : BOOL;
END_VAR

// State entry detection
bStateEntry := (eState <> eStatePrev);
eStatePrev := eState;

CASE eState OF
    ST_IDLE:
        IF bStateEntry THEN
            // Entry actions
        END_IF
        // State logic

    ST_INIT:
        // ...

    ST_ERROR:
        // Error handling

END_CASE
```

### Motion FB Usage (PLCopen)

```iecst
VAR
    fbPower     : MC_Power;
    fbReset     : MC_Reset;
    fbMoveVel   : MC_MoveVelocity;
    fbStop      : MC_Stop;
    fbSetPos    : MC_SetPosition;
    Axis        : AXIS_REF;
END_VAR

// Power on
fbPower(Axis := Axis, Enable := TRUE);

// Move with velocity
fbMoveVel(
    Axis := Axis,
    Execute := TRUE,
    Velocity := 1000.0,  // mm/s
    Acceleration := 5000.0,
    Deceleration := 5000.0
);

// Stop
fbStop(Axis := Axis, Execute := TRUE, Deceleration := 5000.0);
```

## Homing Sequence State Machine

For incremental encoder systems (power-up calibration required):

```iecst
TYPE E_HomeScanState :
(
    ST_IDLE,
    ST_WAIT_SAFE,
    ST_RESET_FAULT,
    ST_POWER_ON,
    ST_SET_SAFE_LIMITS,
    ST_MOVE_TO_NEG_LIM,
    ST_STOP_AT_NEG_LIM,
    ST_SEEK_HOME_FROM_NEG,
    ST_SET_HOME_POS,
    ST_MOVE_TO_POS_LIM,
    ST_STOP_AT_POS_LIM,
    ST_RETURN_HOME,
    ST_DONE,
    ST_ERROR,
    ST_ABORT_STOP
);
END_TYPE
```

### Calibration Flow

1. **Move to Negative Limit** (low speed, 100-300 mm/s)
2. **Seek Home from Negative** (set position to 0.0 mm)
3. **Move to Positive Limit** (verify full travel)
4. **Return to Home** (verify repeatability)

## Offline Development

### Local Simulation (No Hardware)

1. Create project without scanning real devices
2. Add simulated axis: NC Configuration → Add Simulated Axis
3. Set target system to local (127.0.0.1.1.1)
4. Activate configuration and run

### Remote Connection to C6030

1. Add route to C6030 (via AMS Router)
2. Set target system to C6030 AMS NetId
3. Use Remote Manager if version mismatch

## Task Configuration

### Recommended Settings

| Task | Cycle Time | Priority | Purpose |
|------|------------|----------|---------|
| PlcTask | 1 ms | 20 | PLC logic |
| NcTask | 1 ms | 1 | Motion control |

### DC Synchronization

Ensure PlcTask and NcTask cycles match EtherCAT DC Sync0 period.

## Debugging Tips

### Watch Variables

- `Axis.NcToPlc.ActPos` - Actual position
- `Axis.NcToPlc.ActVelo` - Actual velocity
- `Axis.NcToPlc.StateDWord` - Axis state bits
- `Axis.Status.Error` - Error flag

### Common Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Axis not moving | Not in OP state | Check EtherCAT state |
| Following error | Wrong scaling | Verify inc/mm ratio |
| Limit error | Wrong limit config | Check software limits |
| DC sync error | Cycle mismatch | Align task cycles |

## Available References

- `references/project-evaluation.md` - TwinCAT project evaluation report for LMSA13L
- `references/solution-requirements.md` - Complete TwinCAT solution requirements
- `references/beckhoff-tutorial.md` - Beckhoff Taiwan EtherCAT tutorial notes
- `references/extension-libraries.md` - Extension library descriptions
- `references/spt-libraries.md` - SPT-Libraries (Beckhoff USA Community) details
- `references/csvfilelib.md` - CSVFileLib (Loupe Team) documentation
- `references/github-repos-analysis.md` - Analysis of Loupe Team and Beckhoff USA GitHub repos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhlee0328) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
