---
name: knx-xml-context
description: Context loader for KNX XML development. Use when user says "knx context", "load knx", "knx validation", "knx xml rules", or starts working on KNX XML/knxprod files. Use when this capability is needed.
metadata:
  author: funnyhust
---

# KNX XML Development Context

This skill provides instant context for KNX XML development, including validation rules, crash points, and optimization best practices from Kaenx Creator source code analysis.

## When to Use This Skill

- Load context for KNX XML development in new conversations
- Check validation rules before importing to Kaenx Creator
- Design Dynamic sections for ETS UI
- Debug import errors in Kaenx Creator
- Create or modify `.knxprod` files
- NOT for general XML editing (use standard XML tools)

## Prerequisites

- Python 3.x (for validation script)
- Access to `POC_KNX_XML_Source/` directory

## How to Use

### Step 1: Load Context

Simply mention "knx context" or "knx validation" in your message, and this skill will be activated.

### Step 2: Reference Quick Cards

Use the quick reference cards below for common validation rules.

### Step 3: Run Validation (Optional)

```bash
python POC_KNX_XML_Source/Validate_Tool/validate_knx_comprehensive.py <xml_file>
```

### Step 4: Consult Strict Rules
Always check: `POC_KNX_XML_Source/XML_Import_Rule/Rule.md`

## Quick Reference Cards

### Critical Crash Points (Must Avoid)

| Issue | Requirement | Error Type |
|-------|-------------|------------|
| `Hardware.VersionNumber` | Must exist (NOT `Version`) | NullReferenceException |
| `Hardware2ProgramRefId` suffix | ≥13 chars after `_HP-` | ArgumentOutOfRangeException |
| All ID suffixes | Must be **numeric only** | FormatException |
| `Manufacturer.RefId` | ≥3 characters | OutOfRangeException |
| `RefId` duplicates | **Must be UNIQUE** globally | Import Error (Nicht eindeutig) |
| OrderNumber | **Alphanumeric only** (no special chars) | Encoding Warning |

### ID Format Rules

| Element | Offset | Valid Example | Invalid Example |
|---------|--------|---------------|-----------------|
| Parameter | 2 | `..._P-123` | `..._P-Btn1` ❌ |
| ParameterRef | 2 | `..._R-456` | `..._R-Mode` ❌ |
| ComObject | 0 | `..._O-1` | `..._O-Switch` ❌ |
| ModuleDef | 3 | `..._MD-1` | `..._MD-Main` ❌ |
| ParameterBlock | 3 | `..._PB-1` | `..._PB-Config` ❌ |

### Hardware Mandatory Attributes

```xml
<Hardware 
    Name="DeviceName"
    SerialNumber="1234567890"
    VersionNumber="1"             <!-- MANDATORY -->
    BusCurrent="10"               <!-- MANDATORY -->
    HasIndividualAddress="true"   <!-- MANDATORY -->
    HasApplicationProgram="true"  <!-- MANDATORY -->
/>
```

### Character Encoding in IDs

| Char | Encoded | Char | Encoded |
|------|---------|------|---------|
| `-` | `.2D` | `.` | `.2E` |
| ` ` | `.20` | `/` | `.2F` |
| `+` | `.2B` | `,` | `.2C` |

### DatapointType & ObjectSize Formats

```xml
<ComObject DatapointType="DPST-1-1" />  <!-- Switch On/Off -->
<ComObject DatapointType="DPT-9" />      <!-- 2-Byte Float -->
<ComObject ObjectSize="1 Bit" />
<ComObject ObjectSize="2 Bytes" />
```

## Examples

### Example 1: Validate Before Import

```bash
python validate_knx_comprehensive.py my_device.xml
```

Output:
```
✓ PASSED (12): XML syntax, Hardware.VersionNumber...
⚠️ WARNINGS (2): Missing BusCurrent...
❌ ERRORS (1): Parameter ID has non-numeric suffix
RESULT: FAIL - 1 error(s) will cause import failure
```

### Example 2: Dynamic Section with Master Switch

```xml
<Channel Name="Channel 1" Number="1">
    <ParameterBlock Id="PB-1" Name="CH1_Config">
        <ParameterRefRef RefId="P_CH1_Mode_Ref" />
        <choose ParamRefId="P_CH1_Mode_Ref">
            <when test="0" />  <!-- Disabled: empty -->
            <when test="1">    <!-- Switch Mode -->
                <ParameterRefRef RefId="P_Switch_Delay_Ref" />
                <ComObjectRefRef RefId="O_Switch_Ref" />
            </when>
        </choose>
    </ParameterBlock>
</Channel>
```

## Best Practices

### 5 Golden Rules for Dynamic Section

1. **Default to Hidden**: Always wrap refs in `<choose><when>`
2. **Master Switch Pattern**: Every block starts with Enum, value `0` = Disabled
3. **Nesting is King**: Use nested `<choose>` for hierarchical UI
4. **Use Comparison Operators**: `test="<3"`, `test=">0"` for flexible conditions
5. **Conflict Prevention**: Check GPIO state before showing options

### ID Naming & Consistency (Golden Rule)

- ✅ Use numeric suffixes: `_P-1`, `_O-2`, `_R-3`
- ✅ **Deep Referential Integrity**: Cross-check every `RefId`, `ParameterType`, and `ParamRefId`.
- ❌ Avoid text suffixes: `_P-Button`, `_O-Switch`
- ⚠️ **Zero Typo Risk**: Even a single extra `0` in a reference suffix (e.g., `00001` vs `000001`) will trigger a "Sequence contains no matching element" crash in ETS/Kaenx.

### Hardware2ProgramRefId Format

```
✅ ..._HP-0000-00-00001   (13 chars after _HP-)
❌ ..._HP-1-1-1           (5 chars - CRASH!)
```

## Troubleshooting

| Error Message | Root Cause | Solution |
|---------------|------------|----------|
| Unknown Error (Crash) | NullReference or OutOfRange | Check Hardware.VersionNumber exists |
| Input string was not in correct format | Non-numeric ID suffix | Rename IDs to use numbers only |
| Sequence contains no matching element | **Broken Reference (RefId Typo)** | Verify all `ParameterType` and `RefId` suffixes match exactly |
| Index outside bounds | String too short | Pad Hardware2ProgramRefId to 13+ chars |

## Resources

For detailed documentation, see:

- [Validation Specification](../../POC_KNX_XML_Source/Validate_Tool/KNX_Validation_Specification.md) - Full 46+ crash points
- [Python Validator](../../POC_KNX_XML_Source/Validate_Tool/validate_knx_comprehensive.py) - Automated validation script
- [Optimization Guide](../../POC_KNX_XML_Source/KNX_Optimization/KNX_Optimization_Guide.md) - Best practices with case study

## Workflow Summary

```
1. Design XML    → Follow Optimization Guide
2. Validate      → Run Python script
3. Import        → Kaenx Creator
4. Export        → .knxprod file
5. Test          → ETS
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/funnyhust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
