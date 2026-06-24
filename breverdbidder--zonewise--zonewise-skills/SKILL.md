---
name: zonewise-skills
description: Browser automation skills for navigating municipal permit portals across 17 Brevard County jurisdictions. Use for permit searches, zoning verification, site plan status, and development review tracking. Trigger phrases include "permit search", "zoning verification", "site plan status", "development review", "building permit". Use when this capability is needed.
metadata:
  author: breverdbidder
---

# ZoneWise Skills Library

**Permit portal automation for 17 Brevard County jurisdictions.**

## Coverage

### Primary Jurisdictions (Implemented)

| Jurisdiction | Portal Type | Skills |
|--------------|-------------|--------|
| **Brevard County** | Citizen Access | 3 skills |
| **Melbourne** | Tyler ERP / Accela | 2 skills |
| **Palm Bay** | Custom | 2 skills |
| **Satellite Beach** | Custom + Municode | 2 skills |

### Secondary Jurisdictions (Planned)

| Jurisdiction | Portal | Status |
|--------------|--------|--------|
| Titusville | Citizen Access | Q1 2026 |
| Cocoa | Tyler ERP | Q1 2026 |
| Rockledge | Custom | Q1 2026 |
| Indian Harbour Beach | Custom | Q1 2026 |
| Indialantic | Custom | Q1 2026 |
| Melbourne Beach | Custom | Q1 2026 |
| Cape Canaveral | Custom | Q1 2026 |
| Cocoa Beach | Custom | Q1 2026 |
| West Melbourne | Citizen Access | Q1 2026 |
| Malabar | Custom | Q2 2026 |
| Grant-Valkaria | Custom | Q2 2026 |
| Palm Shores | Custom | Q2 2026 |
| Melbourne Village | Custom | Q2 2026 |

## Skills by Jurisdiction

### 🏛️ BREVARD COUNTY (Unincorporated)

| Skill | Description | Variables |
|-------|-------------|-----------|
| `brevard_permit_search` | Search permits by address | `street_address` |
| `brevard_permit_detail` | Get full permit details | `cap_id1`, `cap_id2`, `cap_id3` |
| `brevard_zoning` | Verify zoning via GIS | `parcel_id` |

**Portal:** https://egov.brevardfl.gov/CitizenAccess/

### 🏛️ MELBOURNE

| Skill | Description | Variables |
|-------|-------------|-----------|
| `melbourne_permit_search` | Search building permits | `street_address` |
| `melbourne_site_plan` | Site plan review status | `application_number` |

**Portal:** https://aca-prod.accela.com/MELBOURNE/

### 🏛️ PALM BAY

| Skill | Description | Variables |
|-------|-------------|-----------|
| `palmbay_permit_search` | Search building permits | `street_address` |
| `palmbay_dev_review` | Development review status | - |

**Portal:** https://palmbayflorida.org/

### 🏛️ SATELLITE BEACH (Home Jurisdiction)

| Skill | Description | Variables |
|-------|-------------|-----------|
| `satbeach_permit_search` | Search permits | - |
| `satbeach_zoning` | Zoning code lookup | - |

**Portal:** https://www.satellitebeach.org/ + Municode

### 🌐 MULTI-JURISDICTION

| Skill | Description | Variables |
|-------|-------------|-----------|
| `multi_permit_search` | Search all jurisdictions | `street_address`, `parcel_id` |
| `zoning_comparison` | Compare zoning across jurisdictions | `parcel_id` |

## Usage Examples

### Basic Permit Search

```python
from zonewise_skills_library import ZoneWiseSkillsLibrary

library = ZoneWiseSkillsLibrary()

# Search Brevard County permits
result = operator.play_skill(
    library.brevard_county.permit_search_by_address(),
    variables={"street_address": "123 Main St"}
)

# Get permit details
detail = operator.play_skill(
    library.brevard_county.permit_detail_lookup(),
    variables={"cap_id1": "BLD", "cap_id2": "2025", "cap_id3": "00001"}
)
```

### Zoning Verification

```python
# Verify zoning via GIS
zoning = operator.play_skill(
    library.brevard_county.zoning_verification(),
    variables={"parcel_id": "12-34-56-78-90"}
)

# Output: {zoning_code: "RU-1", future_land_use: "Residential", ...}
```

### Site Plan Status

```python
# Check Melbourne site plan review
status = operator.play_skill(
    library.melbourne.site_plan_status(),
    variables={"application_number": "SP-2025-001"}
)
```

### Multi-Jurisdiction Search

```python
# Search all jurisdictions in parallel
results = operator.play_skill(
    library.multi.comprehensive_permit_search(),
    variables={"street_address": "123 Main St"}
)

# Returns aggregated results from all jurisdictions
```

## Integration with ZoneWise Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│              ZONEWISE 4-AGENT PIPELINE                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Stage 1: Discovery Agent                                   │
│  └── Identify jurisdiction from parcel/address             │
│                                                             │
│  Stage 2: Zoning Agent (Uses ZoneWise Skills)              │
│  ├── Run brevard_zoning → Get zoning code                  │
│  ├── Fetch ordinance text → Parse requirements             │
│  └── Calculate buildable envelope                          │
│                                                             │
│  Stage 3: BROWSER AGENT (Uses ZoneWise Skills)             │
│  ├── Run permit_search → Check existing permits            │
│  ├── Run site_plan_status → Check approvals                │
│  └── Run multi_permit_search → Cross-reference             │
│                                                             │
│  Stage 4: QA Agent                                          │
│  └── Validate all findings                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Portal Types

### Citizen Access (Accela)

Most common portal type in Florida. Skills target standard Accela selectors.

```python
# Standard Accela selectors
selectors = {
    "search_input": "#ctl00_PlaceHolderMain_generalSearchForm_txtGSAddress",
    "search_button": "#ctl00_PlaceHolderMain_btnNewSearch",
    "results_grid": ".ACA_Grid_OverFlow"
}
```

### Tyler ERP / EnerGov

Used by Melbourne, some larger cities.

### Custom Portals

Smaller cities often have custom or WordPress-based portals. Requires individual skill development.

### Municode

Zoning ordinance lookup. Uses web_search + web_fetch approach for JS-rendered content.

## Smart Router Integration

| Operation | Model Tier | Cost |
|-----------|-----------|------|
| Portal navigation | FREE (Gemini) | $0 |
| Form filling | FREE (Gemini) | $0 |
| Data extraction | FREE (Gemini) | $0 |
| Zoning interpretation | BALANCED (Sonnet) | $ |
| Complex analysis | PREMIUM (Opus) | $$ |

**Result:** 85%+ in FREE tier

## Error Handling

```python
error_handlers=[
    {"condition": "no_results", "action": "return_empty_list"},
    {"condition": "page_timeout", "action": "retry_once"},
    {"condition": "login_required", "action": "flag_for_credentials"},
    {"condition": "captcha_detected", "action": "flag_for_manual"},
    {"condition": "portal_down", "action": "use_fallback_source"}
]
```

## Output Fields

All skills define expected output fields:

```python
output_fields=[
    "permit_number",
    "permit_type", 
    "status",
    "issue_date",
    "expiration_date",
    "contractor",
    "valuation",
    "inspections"
]
```

## Q1 2026 Roadmap

1. **Complete 17 jurisdictions** - All Brevard municipalities
2. **Add login support** - For portals requiring accounts
3. **Expand to Orange County** - Next county target
4. **Build skill recorder** - Let users create custom portal skills

---

**11 skills for 4 jurisdictions. Q1 2026: 17 jurisdictions complete.**

*Built by Claude AI Architect + ZoneWise.AI*  
*We are the team of Claude innovators!*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/breverdbidder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
