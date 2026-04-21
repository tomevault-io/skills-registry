---
name: troubleshooting-docs
description: Capture problem solutions in searchable knowledge base Use when this capability is needed.
metadata:
  author: davidorex
---

# troubleshooting-docs Skill

**Purpose:** Automatically document solved problems to build searchable institutional knowledge with category-based organization (enum-validated problem types).

## Overview

This skill captures problem solutions immediately after confirmation, creating structured documentation that serves as a searchable knowledge base for future sessions. Documentation is organized by symptom category, enabling fast lookup for VCV Rack development issues.

**Why documentation matters:**

When researching problems, you can quickly find solutions by symptom:

- "What causes build failures with Rack SDK?" → Search troubleshooting/build-failures/
- "Why are module parameters not saving?" → Search troubleshooting/parameter-issues/
- "How to fix SVG panel rendering?" → Search troubleshooting/panel-issues/

All documentation is searchable and provides forensic evidence for future development.

---

## 7-Step Process

### Step 1: Detect Confirmation

**Auto-invoke after phrases:**

- "that worked"
- "it's fixed"
- "working now"
- "problem solved"
- "that did it"

**OR manual:** `/doc-fix` command

**Non-trivial problems only:**

- Multiple investigation attempts needed
- Tricky debugging that took time
- Non-obvious solution
- Future sessions would benefit

**Skip documentation for:**

- Simple typos
- Obvious syntax errors
- Trivial fixes immediately corrected

### Step 2: Gather Context

Extract from conversation history:

**Required information:**

- **Module name**: Which module had the problem
- **Symptom**: Observable error/behavior (exact error messages)
- **Investigation attempts**: What didn't work and why
- **Root cause**: Technical explanation of actual problem
- **Solution**: What fixed it (code/config changes)
- **Prevention**: How to avoid in future

**Environment details:**

- Rack SDK version
- Stage (0-6 or post-implementation)
- OS version
- File/line references

**Ask user if missing critical context:**

```
I need a few details to document this properly:

1. Which module had this issue? [ModuleName]
2. What was the exact error message or symptom?
3. What stage were you in? (0-6 or post-implementation)

[Continue after user provides details]
```

### Step 3: Check Existing Docs

Search troubleshooting/ for similar issues:

```bash
# Search by error message keywords
grep -r "exact error phrase" troubleshooting/

# Search by symptom category
ls troubleshooting/[category]/
```

**If similar issue found:**

Present options:

```
Found similar issue: troubleshooting/build-failures/similar-issue.md

What's next?
1. Create new doc with cross-reference (recommended)
2. Update existing doc - Add this case as variant
3. Link as duplicate - Don't create new doc
4. Other
```

### Step 4: Generate Filename

Format: `[sanitized-symptom]-[module]-[YYYYMMDD].md`

**Sanitization rules:**

- Lowercase
- Replace spaces with hyphens
- Remove special characters except hyphens
- Truncate to reasonable length (< 80 chars)

**Examples:**

- `rack-sdk-linker-error-SimpleOsc-20251112.md`
- `svg-panel-not-loading-WaveShaper-20251112.md`
- `parameter-state-not-saving-Reverb-20251112.md`

### Step 5: Validate YAML Schema

**CRITICAL:** All docs require validated YAML frontmatter with enum validation.

**VCV Rack Problem Types (enums):**

```yaml
problem_type:
  - build_error        # Compilation/linking failures
  - runtime_error      # Crashes, exceptions during execution
  - panel_issue        # SVG rendering, layout problems
  - cv_processing      # CV signal handling issues
  - dsp_issue          # Audio processing problems
  - parameter_issue    # Module parameter bugs
  - port_issue         # Input/output jack problems
  - performance        # CPU/memory optimization
  - sdk_integration    # Rack SDK API misuse

component:
  - rack_sdk           # Rack SDK core
  - plugin_json        # plugin.json configuration
  - helper_py          # helper.py script
  - svg_panel          # SVG panel artwork
  - module_widget      # ModuleWidget implementation
  - module_struct      # Module struct (DSP)
  - cmake              # CMake build system
  - dsp_processor      # DSP processing logic
  - cv_ports           # Input/output ports
  - parameters         # Module parameters

root_cause:
  - missing_sdk_path   # Rack SDK not found
  - wrong_api_usage    # Incorrect Rack API usage
  - svg_constraint     # SVG doesn't meet VCV requirements
  - cv_scaling         # Incorrect CV voltage scaling
  - missing_config     # Missing plugin.json entry
  - helper_py_error    # helper.py generation failure
  - memory_issue       # Memory leak or allocation
  - threading_issue    # Thread safety violation
  - logic_error        # DSP algorithm bug
  - version_mismatch   # SDK version incompatibility

resolution_type:
  - code_fix           # Code change required
  - config_change      # Configuration update
  - sdk_update         # SDK version upgrade
  - build_fix          # Build system correction

severity:
  - critical           # Blocks development completely
  - moderate           # Significant impact, workaround exists
  - minor              # Cosmetic or edge case
```

**Required fields:**
- ✅ `module` - String (module name or "VCV_Rack")
- ✅ `date` - String (YYYY-MM-DD format)
- ✅ `problem_type` - Enum from schema
- ✅ `component` - Enum from schema
- ✅ `symptoms` - Array (1-5 items)
- ✅ `root_cause` - Enum from schema
- ✅ `resolution_type` - Enum from schema
- ✅ `severity` - Enum from schema

**Optional fields:**
- `rack_sdk_version` - String (X.Y.Z format)
- `tags` - Array of strings

**Validation process:**

```bash
# Verify enum values against schema
# problem_type must be in: build_error, runtime_error, panel_issue, cv_processing,
#   dsp_issue, parameter_issue, port_issue, performance, sdk_integration

# component must be in: rack_sdk, plugin_json, helper_py, svg_panel, module_widget,
#   module_struct, cmake, dsp_processor, cv_ports, parameters

# root_cause must be in: missing_sdk_path, wrong_api_usage, svg_constraint,
#   cv_scaling, missing_config, helper_py_error, memory_issue, threading_issue,
#   logic_error, version_mismatch

# resolution_type must be in: code_fix, config_change, sdk_update, build_fix

# severity must be in: critical, moderate, minor
```

**BLOCK if validation fails:**

```
❌ YAML validation failed

Errors:
- problem_type: must be one of schema enums, got "compilation_error"
- severity: must be one of [critical, moderate, minor], got "high"
- symptoms: must be array with 1-5 items, got string

Please provide corrected values.
```

Present retry with corrected values, don't proceed until valid.

### Step 6: Create Documentation

**Determine category from problem_type enum:**

Category mapping (based on validated `problem_type` field):

- `build_error` → troubleshooting/build-failures/
- `runtime_error` → troubleshooting/runtime-issues/
- `panel_issue` → troubleshooting/panel-issues/
- `cv_processing` → troubleshooting/cv-issues/
- `dsp_issue` → troubleshooting/dsp-issues/
- `parameter_issue` → troubleshooting/parameter-issues/
- `port_issue` → troubleshooting/port-issues/
- `performance` → troubleshooting/performance/
- `sdk_integration` → troubleshooting/sdk-integration/

**Create documentation file:**

```bash
PROBLEM_TYPE="[from validated YAML]"
CATEGORY="[mapped from problem_type]"
FILENAME="[generated-filename].md"
DOC_PATH="troubleshooting/${CATEGORY}/${FILENAME}"

# Create directory if needed
mkdir -p "troubleshooting/${CATEGORY}"

# Write documentation using template
cat > "$DOC_PATH" << 'EOF'
---
module: [ModuleName or "VCV_Rack"]
date: [YYYY-MM-DD]
problem_type: [validated enum value]
component: [validated enum value]
symptoms:
  - [Observable symptom 1]
  - [Observable symptom 2]
root_cause: [validated enum value]
resolution_type: [validated enum value]
severity: [validated enum value]
tags: [keywords]
---

[Documentation content from template]
EOF
```

**Documentation template:**

```markdown
---
module: SimpleOsc
date: 2025-11-12
problem_type: build_error
component: rack_sdk
symptoms:
  - "Linker error: undefined reference to 'rack::plugin'"
  - "Build fails at linking stage"
root_cause: missing_sdk_path
resolution_type: config_change
severity: critical
rack_sdk_version: 2.5.2
tags: [build, cmake, sdk]
---

# Rack SDK Linker Error - Undefined Reference

## Problem

Linker fails with undefined reference to Rack SDK symbols during module build.

## Symptom

**Error message:**
```
/usr/bin/ld: build/src/SimpleOsc.cpp.o: undefined reference to 'rack::plugin::Plugin::Plugin()'
/usr/bin/ld: build/src/SimpleOsc.cpp.o: undefined reference to 'rack::app::ModuleWidget::ModuleWidget()'
collect2: error: ld returned 1 exit status
```

**Observable behavior:**
- Compilation succeeds
- Linking fails immediately
- No plugin binary generated

## Context

- **Module:** SimpleOsc
- **Stage:** Stage 2 (Foundation)
- **Rack SDK Version:** 2.5.2
- **OS:** macOS 14.3
- **Build system:** CMake via Rack SDK Makefile

## Investigation

**Attempts that didn't work:**

1. **Clean rebuild** - Ran `make clean && make`, same error
2. **Check Rack SDK installation** - SDK present in expected location
3. **Verify include paths** - Headers found correctly (compilation succeeds)

**What led to solution:**

- Noticed linker can't find libRack symbols
- Checked RACK_DIR environment variable
- Found RACK_DIR not set in shell environment

## Root Cause

**Technical explanation:**

Rack SDK Makefile expects `RACK_DIR` environment variable to locate SDK installation. Without it, linker doesn't know where to find libRack library for symbol resolution.

The SDK's `compile.mk` includes:
```make
RACK_DIR ?= /path/to/Rack-SDK
LDFLAGS += -L$(RACK_DIR)
```

Without RACK_DIR set, `-L` flag has empty path, causing linker failure.

## Solution

**Set RACK_DIR environment variable permanently:**

```bash
# Add to ~/.zshrc or ~/.bashrc
export RACK_DIR="/Users/david/Projects/Rack-SDK"

# Reload shell configuration
source ~/.zshrc

# Verify
echo $RACK_DIR  # Should output: /Users/david/Projects/Rack-SDK
```

**Then rebuild:**
```bash
cd modules/SimpleOsc
make clean
make
```

**Result:** Build succeeds, plugin binary generated.

## Prevention

**How to avoid this in future:**

1. **Always set RACK_DIR** before starting new module development
2. **Add to shell profile** - Permanent environment configuration
3. **Document in setup instructions** - Include in project README
4. **Verify in /setup command** - System setup should check RACK_DIR

**System setup check:**
```bash
if [ -z "$RACK_DIR" ]; then
    echo "ERROR: RACK_DIR not set"
    echo "Set with: export RACK_DIR=/path/to/Rack-SDK"
    exit 1
fi
```

## Related Issues

- None yet (first occurrence)

## References

- VCV Rack SDK documentation: Building plugins
- Rack SDK compile.mk source code
- Environment variable configuration guide
```

### Step 7: Cross-Reference & Critical Pattern Detection

If similar issues found in Step 3:

**Update existing doc:**

```bash
# Add Related Issues link to similar doc
echo "- See also: [$FILENAME]($DOC_PATH)" >> [similar-doc.md]
```

**Update new doc:**
Already includes cross-reference from Step 6.

**Update patterns if applicable:**

If this represents a common pattern (3+ similar issues):

```bash
# Add to troubleshooting/patterns/common-solutions.md
cat >> troubleshooting/patterns/common-solutions.md << 'EOF'

## [Pattern Name]

**Common symptom:** [Description]
**Root cause:** [Technical explanation]
**Solution pattern:** [General approach]

**Examples:**
- [Link to doc 1]
- [Link to doc 2]
- [Link to doc 3]
EOF
```

**Critical Pattern Detection (Optional Proactive Suggestion):**

If this issue has automatic indicators suggesting it might be critical:
- Severity: `critical` in YAML
- Affects multiple modules OR foundational stage (Stage 2 or 3)
- Non-obvious solution

Then in the decision menu (Step 8), add a note:
```
💡 This might be worth adding to Required Reading (Option 2)
```

But **NEVER auto-promote**. User decides via decision menu (Option 2).

**Template for critical pattern addition:**

When user selects Option 2 (Add to Required Reading):

```markdown
## N. [Pattern Name] (ALWAYS REQUIRED)

### ❌ WRONG ([Will cause X error])
```[language]
[code showing wrong approach]
```

### ✅ CORRECT
```[language]
[code showing correct approach]
```

**Why:** [Technical explanation of why this is required]

**Placement/Context:** [When this applies]

**Documented in:** `troubleshooting/[category]/[filename].md`

---
```

---

## Decision Menu After Capture

After successful documentation:

```
✓ Solution documented

File created: troubleshooting/[category]/[filename].md

What's next?
1. Continue workflow (recommended)
2. Add to Required Reading - Promote to critical patterns (vcv-critical-patterns.md)
3. Link related issues - Connect to similar problems
4. Update common patterns - Add to pattern library
5. View documentation - See what was captured
6. Other
```

**Handle responses:**

**Option 1: Continue workflow**

- Return to calling skill/workflow
- Documentation is complete

**Option 2: Add to Required Reading** ⭐ PRIMARY PATH FOR CRITICAL PATTERNS

User selects this when:
- System made this mistake multiple times across different modules
- Solution is non-obvious but must be followed every time
- Foundational requirement (Rack SDK, CMake, SVG panels, etc.)

Action:
1. Extract pattern from the documentation
2. Format as ❌ WRONG vs ✅ CORRECT with code examples
3. Add to `troubleshooting/patterns/vcv-critical-patterns.md`
4. Add cross-reference back to this doc
5. Confirm: "✓ Added to Required Reading. All subagents will see this pattern before code generation."

**Option 3: Link related issues**

- Prompt: "Which doc to link? (provide filename or describe)"
- Search troubleshooting/ for the doc
- Add cross-reference to both docs
- Confirm: "✓ Cross-reference added"

**Option 4: Update common patterns**

- Check if 3+ similar issues exist
- If yes: Add pattern to troubleshooting/patterns/common-solutions.md
- If no: "Need 3+ similar issues to establish pattern (currently N)"

**Option 5: View documentation**

- Display the created documentation
- Present decision menu again

**Option 6: Other**

- Ask what they'd like to do

---

## Integration Points

**Invoked by:**

- Auto-detection after success phrases
- `/doc-fix` command
- Any skill after solution confirmation
- Manual: "document this solution"

**Invokes:**

- None (terminal skill)

**Reads:**

- Conversation history (for context extraction)
- `MODULES.md` (validate module name)
- `troubleshooting/` (search existing docs)

**Creates:**

- `troubleshooting/[category]/[filename].md` (documentation file)
- Updates to `troubleshooting/patterns/common-solutions.md` (if pattern detected)
- Updates to `troubleshooting/patterns/vcv-critical-patterns.md` (if promoted to Required Reading)

**Updates:**

- Existing docs (cross-references)
- Pattern library (if applicable)

---

## Success Criteria

Documentation is successful when:

- ✅ YAML frontmatter validated (all required fields, correct formats)
- ✅ File created in troubleshooting/[category]/
- ✅ Documentation follows template structure
- ✅ All sections populated with relevant content
- ✅ Code examples included (if applicable)
- ✅ Cross-references added (if similar issues exist)
- ✅ File is searchable (descriptive filename, tags)

---

## Error Handling

**Missing context:**

- Ask user for missing details
- Don't proceed until critical info provided

**YAML validation failure:**

- Show specific errors
- Present retry with corrected values
- BLOCK until valid

**Similar issue ambiguity:**

- Present multiple matches
- Let user choose: new doc, update existing, or link as duplicate

**Module not in MODULES.md:**

- Warn but don't block
- Proceed with documentation
- Suggest: "Add [Module] to MODULES.md if not there"

---

## Notes for Claude

**When executing this skill:**

1. Always validate YAML frontmatter - BLOCK if invalid
2. Extract exact error messages from conversation
3. Include code examples in solution section
4. Cross-reference similar issues automatically
5. Category detection is automatic from problem_type enum
6. Ask user if critical context missing
7. Be specific in documentation (exact file:line, versions)

**Common pitfalls:**

- Forgetting to create directories before writing files
- Missing YAML validation (creates invalid docs)
- Vague descriptions (not searchable)
- No code examples (harder to understand solution)
- No cross-references (knowledge stays siloed)

---

## Quality Guidelines

**Good documentation has:**

- ✅ Exact error messages (copy-paste from output)
- ✅ Specific file:line references
- ✅ Observable symptoms (what you saw, not interpretations)
- ✅ Failed attempts documented (helps avoid wrong paths)
- ✅ Technical explanation (not just "what" but "why")
- ✅ Code examples (before/after if applicable)
- ✅ Prevention guidance (how to catch early)
- ✅ Cross-references (related issues)

**Avoid:**

- ❌ Vague descriptions ("something was wrong")
- ❌ Missing technical details ("fixed the code")
- ❌ No context (which version? which file?)
- ❌ Just code dumps (explain why it works)
- ❌ No prevention guidance
- ❌ No cross-references

---

## VCV Rack Specific Categories

**Build Failures:**
- Rack SDK not found
- Linker errors
- CMake configuration issues
- helper.py failures

**Panel Issues:**
- SVG not loading
- Component positions wrong
- Panel dimensions incorrect
- helper.py parsing errors

**CV/Signal Issues:**
- Incorrect CV scaling (1V/oct)
- Port not responding
- Signal clipping
- DC offset problems

**DSP Issues:**
- Audio artifacts
- Buffer underruns
- Sample rate handling
- Polyphony bugs

**Parameter Issues:**
- State not saving
- Parameter ranges wrong
- CV modulation not working
- Parameter smoothing issues

**SDK Integration:**
- API misuse
- Version compatibility
- Missing module registration
- plugin.json errors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidorex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
