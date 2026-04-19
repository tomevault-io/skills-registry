---
name: board-pinout-reference
description: How to find and maintain hardware pinout documentation for supported boards Use when this capability is needed.
metadata:
  author: sparkeh9
---

# Board Pinout Reference

## When to Use

- Starting work on a new hardware board
- Needing GPIO pin assignments for display, touch, I2C, SPI, audio, etc.
- Integrating a new peripheral or driver
- Encountering pin conflicts or hardware init failures

## Step 1: Check Existing Docs

**Always check `docs/` first.** This is the single source of truth for hardware configuration:

```powershell
// turbo
Get-ChildItem -Path "docs" -Filter "*.md" | ForEach-Object { Write-Host "  $_" }
```

If a reference file exists for your board (e.g., `docs/hardware-reference.md`), read it — it has:
- Complete GPIO pin table
- Bus configurations (SPI mode, I2C addresses, clock speeds)
- Display initialization sequences
- Known BSP compatibility issues
- Component dependency list

## Step 2: If No Docs Exist — Research Workflow

If you're targeting a board that isn't documented yet, follow this order to gather pin information efficiently:

### 2a. Check BSP Source (if available)

Board Support Packages contain pin definitions in header files:

```powershell
// turbo
# Search for pin definitions in managed_components
Select-String -Path "managed_components" -Include "*.h" -Pattern "GPIO_NUM_" -Recurse | Select-Object -First 30
```

### 2b. Check Manufacturer Examples

Look for example code from the board manufacturer (usually on GitHub). For Waveshare boards:
- Repository: `github.com/waveshareteam/Waveshare-ESP32-components`
- BSP headers contain `#define` pin assignments

### 2c. Check Schematic/Datasheet

If pins aren't in code, check the manufacturer wiki/schematic PDF. Note that some pins (like LCD/touch reset) may be routed through an IO expander rather than direct GPIO.

## Step 3: Document What You Find

Create or update `docs/hardware-reference.md` with the following sections:

```markdown
# [Board Name] Hardware Reference

## Board Overview
- MCU, Flash, PSRAM specs
- Display type, resolution, controller
- Touch controller type, interface
- Other peripherals

## GPIO Pin Assignments
| Function | GPIO | Notes |
|---|---|---|

## Bus Configurations
- SPI host, mode, clock speed
- I2C port, address, speed

## Init Sequences
- Display initialization commands

## BSP Compatibility Notes
- Known issues with specific IDF versions
- Macro compatibility warnings
- Workarounds

## Component Dependencies
| Component | Version | Purpose |
|---|---|---|
```

## Currently Documented Boards

| Board | Doc File | Status |
|---|---|---|
| Waveshare ESP32-S3-Touch-AMOLED-1.8 | `docs/hardware-reference.md` | Complete |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sparkeh9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
