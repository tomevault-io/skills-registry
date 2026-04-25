---
name: cree
description: Cree LED MPN encoding patterns, suffix decoding, and handler guidance. Use when working with high-power LEDs, XLamp series, and SiC power devices. Use when this capability is needed.
metadata:
  author: cantara
---

# Cree Manufacturer Skill

## Company Overview

Cree, Inc. (now Wolfspeed for SiC/power and branded as Cree LED after the 2021 spin-off) is known for:

- **High-Power LEDs**: XLamp series, industry-standard for lighting applications
- **Silicon Carbide (SiC)**: Power devices for EVs, industrial, and energy applications
- **LED Components**: CLV series, CLVBA series for general illumination

**Note**: Cree LED was acquired by SMART Global Holdings in 2021 and continues to operate as Cree LED. The Wolfspeed brand focuses on SiC power devices.

---

## MPN Structure

Cree LED MPNs follow this general structure:

### CLV Series (Standard LEDs)

```
CLV[SERIES][VARIANT]-[COLOR][BIN]
│   │       │         │     │
│   │       │         │     └── Bin code (brightness/color bin): A, B, C, etc.
│   │       │         └── Color code: FK=warm white, WK=cool white
│   │       └── Variant letter: A, B, etc.
│   └── Series number: 1, 2, 6, etc.
└── CLV = Cree LED Vertical series
```

### CLVBA Series (Bin-Coded LEDs)

```
CLVBA-[COLOR][BIN]
│      │     │
│      │     └── Bin code: A, B, C (brightness bin within color)
│      └── Color family: FK=warm white, WK=cool white
└── CLVBA = Cree LED Vertical Bin-coded A series
```

---

## Example Decoding

```
CLV1A-FKA
│  ││  │ │
│  ││  │ └── A = Brightness bin code
│  ││  └── FK = Warm white (typically 2700-3000K)
│  │└── A = Variant A
│  └── 1 = Series 1 (size/power category)
└── CLV = Cree LED Vertical

CLVBA-FKB
│     │ │
│     │ └── B = Brightness bin B (slightly different from A)
│     └── FK = Warm white color family
└── CLVBA = Cree LED Vertical Bin-coded A series

CLV6A-WKA
│  ││  │ │
│  ││  │ └── A = Brightness bin
│  ││  └── WK = Cool white (typically 5000-6500K)
│  │└── A = Variant A
│  └── 6 = Series 6 (larger/higher power)
└── CLV = Cree LED Vertical
```

---

## Series Prefixes

| Prefix | Type | Description |
|--------|------|-------------|
| CLV | High-power LED | Vertical structure LED, general lighting |
| CLVBA | Bin-coded LED | Specific brightness/color bin coding |
| XLamp | XLamp series | Professional lighting LEDs (XP, XM, XHP series) |

---

## Color Codes

| Code | Color Temperature | Description |
|------|-------------------|-------------|
| FK | 2700K-3000K | Warm white, residential lighting |
| WK | 5000K-6500K | Cool white, commercial/daylight |

**Note**: The current CreeHandler only implements CLV and CLVBA series. XLamp series (XP-E, XM-L, XHP) patterns are not yet registered.

---

## Bin Codes

| Suffix | Meaning |
|--------|---------|
| A | Standard brightness bin |
| B | Slightly different brightness (within tolerance) |
| C | Another brightness bin |

LEDs with the same series but different bin codes (e.g., CLVBA-FKA vs CLVBA-FKB) are considered interchangeable/replacements as they have the same color temperature and similar brightness.

---

## Handler Implementation Notes

### Package Code Extraction

```java
// For CLVBA series, returns "SMD" (Surface Mount Device)
// CLVBA-FKA -> "SMD"
// CLVBA-WKB -> "SMD"

// For other CLV series, returns empty string
// CLV1A-FKA -> ""
```

**Current Limitation**: Package code extraction only handles CLVBA series. Other series return empty string.

### Series Extraction

```java
// For CLVBA series: returns everything except last character (bin code)
// CLVBA-FKA -> "CLVBA-FK"
// CLVBA-WKB -> "CLVBA-WK"

// For other CLV series: returns part before dash
// CLV1A-FKA -> "CLV1A"
// CLV2B-WKA -> "CLV2B"
```

### Replacement Detection

```java
// LEDs with same series are considered official replacements
// (same footprint, similar brightness, same color)
isOfficialReplacement("CLVBA-FKA", "CLVBA-FKB") -> true  // Same series (CLVBA-FK)
isOfficialReplacement("CLVBA-FKA", "CLVBA-WKA") -> false // Different series (FK vs WK)
```

---

## Supported Component Types

| ComponentType | Description |
|---------------|-------------|
| `LED` | Generic LED type |
| `LED_HIGHPOWER_CREE` | Cree-specific high-power LED |
| `LED_STANDARD_KINGBRIGHT` | Inherited from generic LED handler (may not be applicable) |
| `LED_RGB_KINGBRIGHT` | Inherited from generic LED handler (may not be applicable) |
| `LED_SMD_KINGBRIGHT` | Inherited from generic LED handler (may not be applicable) |

**Note**: The getSupportedTypes() includes some Kingbright types that may be legacy or incorrect. The primary types used are `LED` and `LED_HIGHPOWER_CREE`.

---

## Registered Patterns

| Pattern | Component Type | Description |
|---------|----------------|-------------|
| `^CLV.*` | LED | Any CLV series LED |
| `^CLV.*` | LED_HIGHPOWER_CREE | CLV series as Cree-specific |
| `^CLVBA-[A-Z]{3}$` | LED | CLVBA with exactly 3-letter suffix |
| `^CLVBA-[A-Z]{3}$` | LED_HIGHPOWER_CREE | CLVBA as Cree-specific |

---

## Common Part Numbers

### CLV Series

| MPN | Color | Series |
|-----|-------|--------|
| CLV1A-FKA | Warm white | CLV1A |
| CLV1B-FKB | Warm white | CLV1B |
| CLV2A-FKA | Warm white | CLV2A |
| CLV6A-FKA | Warm white | CLV6A |

### CLVBA Series

| MPN | Color | Bin |
|-----|-------|-----|
| CLVBA-FKA | Warm white | A |
| CLVBA-FKB | Warm white | B |
| CLVBA-FKC | Warm white | C |
| CLVBA-WKA | Cool white | A |

---

## XLamp Series (NOT YET IMPLEMENTED)

Cree's professional XLamp series are commonly used but not yet in the handler:

| Series | Description | Example MPN |
|--------|-------------|-------------|
| XP-E | High-efficacy, small footprint | XPEBWT-A1-0000-00F51 |
| XP-E2 | 2nd generation XP-E | XPEBWT-U1-0000-00BE7 |
| XP-G | High-power, 3.45mm x 3.45mm | XPGWHT-L1-0000-00H51 |
| XP-G2 | 2nd generation XP-G | XPG2CWHT-000-0000001 |
| XM-L | High-power, up to 1000 lumens | XMLAWT-00-0000-000LT50E5 |
| XHP35 | Extreme high power, 35W | XHP35A-00-0000-0D0HB230G |
| XHP50 | Extreme high power, 50W | XHP50A-00-0000-0D0BJ20E2 |
| XHP70 | Extreme high power, 70W | XHP70A-00-0000-0D0BP20E5 |

**Future Enhancement**: Add patterns for XLamp series.

---

## Related Files

- Handler: `manufacturers/CreeHandler.java`
- Component types: `LED`, `LED_HIGHPOWER_CREE`
- Test: `handlers/CreeHandlerTest.java`

---

## Learnings & Edge Cases

- **Handler uses HashSet in getSupportedTypes()** - Should be changed to `Set.of()` for consistency with other handlers (known tech debt)
- **Kingbright types in getSupportedTypes()** - The handler includes `LED_STANDARD_KINGBRIGHT`, `LED_RGB_KINGBRIGHT`, `LED_SMD_KINGBRIGHT` which appear to be copy-paste leftovers; only `LED` and `LED_HIGHPOWER_CREE` are actually used by Cree patterns
- **Limited pattern coverage** - Only CLV and CLVBA series are implemented; XLamp (XP-E, XM-L, XHP) series are missing
- **Color codes FK/WK** - FK indicates warm white (2700-3000K), WK indicates cool white (5000-6500K); these are color family codes, not exact CCT
- **Bin codes are interchangeable** - Same series LEDs with different bin codes (A, B, C) are considered replacements since they have the same footprint and similar specs
- **CLVBA pattern is restrictive** - Pattern `^CLVBA-[A-Z]{3}$` requires exactly 3 uppercase letters after dash; may not match all real-world variations
- **SiC power devices not supported** - Cree/Wolfspeed SiC MOSFETs (C3M series) and diodes (C4D series) are not implemented in this handler

<!-- Add new learnings above this line -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cantara) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
