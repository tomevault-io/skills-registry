---
name: esp-idf-component-api-research
description: Structured workflow for researching ESP-IDF component drivers, BSP headers, and struct compatibility before integration Use when this capability is needed.
metadata:
  author: sparkeh9
---

# ESP-IDF Component API Research

## When to Use

- Adding a new ESP-IDF component dependency to `idf_component.yml`
- Integrating a BSP or display/touch driver for the first time
- Encountering compilation errors from component macros (designator ordering, type mismatches)
- Needing to understand a component's struct layout for manual initialization

## Overview

ESP-IDF component macros (especially display driver convenience macros like `SH8601_PANEL_BUS_QSPI_CONFIG`) are often written for C and break in C++ due to:
- **Designator ordering** — C allows any order, C++ requires declaration order
- **Implicit type conversions** — `int` to `gpio_num_t` (enum) is rejected in C++
- **Struct layout changes** across IDF versions — fields added/reordered between versions

This skill provides a systematic approach to avoid wasting time on these issues.

## Step 1: Check Local Docs First

Before any external research, check if the component/board is already documented:

```powershell
// turbo
Get-ChildItem -Path "docs" -Recurse -Filter "*.md" | ForEach-Object { Write-Host $_.FullName }
```

If a hardware reference or component guide exists, **read it first** — it likely has the pinout, struct layouts, and known gotchas.

## Step 2: Find Component Headers Locally

After adding a dependency to `idf_component.yml` and running `idf.py build` (which downloads components), headers are at:

```
managed_components/<registry>__<component>/include/
```

```powershell
// turbo
Get-ChildItem -Path "managed_components" -Recurse -Filter "*.h" | Where-Object { $_.DirectoryName -like "*include*" } | ForEach-Object { $_.FullName }
```

## Step 3: Extract Struct Field Ordering

When using designated initializers in C++, field order **must match** the struct definition. Extract it:

1. Open the component's main header (e.g., `esp_lcd_sh8601.h`)
2. Find the macro you want to use (e.g., `SH8601_PANEL_BUS_QSPI_CONFIG`)
3. Identify which IDF struct it initializes (e.g., `spi_bus_config_t`)
4. Find that struct in the IDF source:

```powershell
// turbo
Select-String -Path "d:\esp\v6.0-beta2\esp-idf\components" -Include "*.h" -Pattern "typedef struct" -Recurse | Select-String "<struct_name>"
```

5. Read the struct and note field order — this is the order your designated initializers must follow.

## Step 4: Validate Macro Compatibility

Before using a component's convenience macro in C++ code:

1. **Read the macro source** — find the macro's `#define` in the component header
2. **Check designator ordering** against the IDF struct definition
3. **Check for int→enum issues** — look for bare `-1` assignments to `gpio_num_t` fields
4. If either issue exists, **write manual initialization** instead of using the macro

### Example: Manual Init Instead of Broken Macro

```cpp
// Instead of: SH8601_PANEL_BUS_QSPI_CONFIG(sclk, d0, d1, d2, d3, max_sz)
// Write manually with correct field order:
const spi_bus_config_t spiBusCfg = {
    .data0_io_num = LCD_DATA0,   // union position [0]
    .data1_io_num = LCD_DATA1,   // union position [1]
    .sclk_io_num = LCD_PCLK,    // position [2]
    .data2_io_num = LCD_DATA2,   // union position [3]
    .data3_io_num = LCD_DATA3,   // union position [4]
    // ... remaining fields in declaration order
};
```

## Step 5: Document Findings

After successfully integrating a component, update `docs/hardware-reference.md` with:
- Pin assignments used
- Any macro compatibility issues and workarounds
- Struct field ordering notes
- Init command sequences

This prevents future agents from repeating the research.

## Known C++ Compatibility Issues (IDF 6.0-beta2)

| Component | Macro | Issue | Workaround |
|---|---|---|---|
| `waveshare/esp_lcd_sh8601` | `SH8601_PANEL_BUS_QSPI_CONFIG` | `sclk_io_num` before `data0_io_num` (wrong order) | Manual `spi_bus_config_t` init |
| `waveshare/esp_lcd_sh8601` | `SH8601_PANEL_IO_QSPI_CONFIG` | `int` to `gpio_num_t` conversion | Manual `esp_lcd_panel_io_spi_config_t` init |
| `espressif/esp-brookesia` | `ESP_BROOKESIA_CHECK_*_EXIT` | Private header, not accessible | Use `assert()` / `ESP_ERROR_CHECK()` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sparkeh9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
