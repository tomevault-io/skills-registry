---
name: capacitor
description: Use when working with capacitor components - adding capacitor patterns, parsing capacitor MPNs, extracting capacitance values, voltage ratings, dielectric types, or package codes from capacitor part numbers.
metadata:
  author: cantara
---

# Capacitor Component Skill

Guidance for working with capacitor components in the lib-electronic-components library.

## Supported Manufacturers & Patterns

| Manufacturer | Handler | MPN Patterns | Example |
|--------------|---------|--------------|---------|
| Murata | `MurataHandler` | `GRM...`, `GCM...`, `KCA...` | `GRM188R71H104KA93D` |
| Samsung | `SamsungHandler` | `CL10B...`, `CL21B...`, `CL31B...` | `CL10B104KB8NNNC` |
| TDK | `TDKHandler` | `CH#...`, `MLF#...` | `C1608X5R1C104K` |
| Yageo | `YageoHandler` | `CC####...` | `CC0603KRX7R9BB104` |
| KEMET | `KemetHandler` | `C####...` | `C0603C104K5RACTU` |
| AVX | `AVXHandler` | `TAJ...`, `TPS...`, `TCJ...` | `TAJB106K016RNJ` |
| Nichicon | `NichiconHandler` | `UUD...`, `UPW...`, `UVR...` | `UUD1C101MCL1GS` |
| Panasonic | `PanasonicHandler` | `ECQ...`, `EEF...`, `ECA...` | `EEEFC1V100P` |

## ComponentTypes

```java
// Base type
ComponentType.CAPACITOR

// Manufacturer-specific types
ComponentType.CAPACITOR_CERAMIC_MURATA
ComponentType.CAPACITOR_CERAMIC_TDK
ComponentType.CAPACITOR_CERAMIC_SAMSUNG
ComponentType.CAPACITOR_CERAMIC_YAGEO
ComponentType.CAPACITOR_CERAMIC_KEMET
ComponentType.CAPACITOR_CERAMIC_AVX
ComponentType.CAPACITOR_TANTALUM_KEMET
ComponentType.CAPACITOR_TANTALUM_AVX
ComponentType.CAPACITOR_ELECTROLYTIC_PANASONIC
ComponentType.CAPACITOR_ELECTROLYTIC_NICHICON
ComponentType.CAPACITOR_FILM_KEMET
ComponentType.CAPACITOR_FILM_PANASONIC
ComponentType.CAPACITOR_POLYMER_AVX
```

## MPN Structure

### Murata GRM Series (MLCC)
```
GRM 188 R7 1H 104 K A93 D
│   │   │  │  │   │ │   │
│   │   │  │  │   │ │   └── Packaging (D=180mm Reel)
│   │   │  │  │   │ └────── Special code
│   │   │  │  │   └──────── Tolerance (K=±10%)
│   │   │  │  └──────────── Value (104=100nF)
│   │   │  └─────────────── Voltage (1H=50V)
│   │   └────────────────── Dielectric (R7=X7R)
│   └────────────────────── Size (188=0603)
└────────────────────────── Series (GRM=General)
```

### KEMET C Series
```
C 0603 C 104 K 5R AC TU
│ │    │ │   │ │  │  │
│ │    │ │   │ │  │  └── Packaging (TU=7" Reel)
│ │    │ │   │ │  └───── Termination (AC=Flex)
│ │    │ │   │ └──────── Voltage (5R=50V)
│ │    │ │   └────────── Tolerance (K=±10%)
│ │    │ └────────────── Value (104=100nF)
│ │    └──────────────── Dielectric (C=X7R)
│ └───────────────────── Size (0603)
└─────────────────────── Series
```

## Dielectric Types

| Code | Type | Temp Range | Capacitance Change |
|------|------|------------|-------------------|
| C0G/NP0 | Class I | -55°C to +125°C | ±30ppm/°C |
| X7R | Class II | -55°C to +125°C | ±15% |
| X5R | Class II | -55°C to +85°C | ±15% |
| Y5V | Class II | -30°C to +85°C | +22%/-82% |

## Adding New Capacitor Patterns

1. In the manufacturer handler's `initializePatterns()`:
```java
registry.addPattern(ComponentType.CAPACITOR, "^NEWCAP[0-9]{4}.*");
registry.addPattern(ComponentType.CAPACITOR_CERAMIC_MANUFACTURER, "^NEWCAP[0-9]{4}.*");
```

2. Add to `getSupportedTypes()`:
```java
types.add(ComponentType.CAPACITOR);
types.add(ComponentType.CAPACITOR_CERAMIC_MANUFACTURER);
```

## Similarity Calculation

`CapacitorSimilarityCalculator` compares:
- Capacitance value
- Voltage rating
- Dielectric type (C0G, X7R, X5R, etc.)
- Package size
- Tolerance

## Common Package Sizes

| Code | Metric | Size (mm) |
|------|--------|-----------|
| 0402 | 1005 | 1.0 x 0.5 |
| 0603 | 1608 | 1.6 x 0.8 |
| 0805 | 2012 | 2.0 x 1.25 |
| 1206 | 3216 | 3.2 x 1.6 |

## Murata Size Codes

| Code | Imperial |
|------|----------|
| 155 | 0402 |
| 188 | 0603 |
| 21 | 0805 |
| 31 | 1206 |
| 32 | 1210 |

---

## Learnings & Quirks

<!-- Record component-specific discoveries, edge cases, and quirks here -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cantara) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
