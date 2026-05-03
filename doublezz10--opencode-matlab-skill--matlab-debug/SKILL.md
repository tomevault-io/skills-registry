---
name: matlab-debug
description: MATLAB debugging skill for running and testing MATLAB scripts with automatic error capture and toolbox detection Use when this capability is needed.
metadata:
  author: doublezz10
---

## What I do

I provide a debugging interface for MATLAB scripts across all domains. I help you:

- **Run MATLAB scripts** - Execute .m files with full error capture
- **Debug errors immediately** - Capture and structure MATLAB errors for OpenCode to fix
- **Validate syntax** - Quick syntax checking without full execution
- **Capture outputs** - Get console output, warnings, and figure information
- **Toolbox verification** - Check required toolboxes before execution

## How to use me

### Quick Debug Commands

```bash
# Run a MATLAB script with full error capture
.opencode/skill/matlab-debug/scripts/matlab-cli.sh run path/to/script.m

# Quick syntax check (no execution)
.opencode/skill/matlab-debug/scripts/matlab-cli.sh check path/to/script.m

# Run with workspace preservation for debugging
.opencode/skill/matlab-debug/scripts/matlab-cli.sh debug path/to/script.m

# Check required toolboxes for neural analysis
npx ts-node .opencode/skill/matlab-debug/scripts/matlab-cli.ts toolboxes
```

### Command Reference

| Command | Description |
|---------|-------------|
| `run [--show-figures] <script>` | Execute MATLAB script with full error capture |
| `check <script>` | Syntax validation without execution |
| `debug [--show-figures] <script>` | Run with workspace preservation for debugging |
| `toolboxes` | Verify required toolboxes for your project |

### Figure Options
- **Default**: Figures hidden during debugging (non-intrusive)
- **`--show-figures`**: Display figures for visual debugging and plot optimization

## Examples

### Basic Script Execution

```bash
$ .opencode/skill/matlab-debug/scripts/matlab-cli.sh run Analysis/neurons/08_credit_assignment/analyses/test_credit_assignment.m

=== MATLAB Execution Results ===
Script: test_credit_assignment.m
Status: ERROR
Duration: 2.3s

=== Error Details ===
File: Analysis/neurons/08_credit_assignment/analyses/helpers/load_spkpools.m
Line: 45
Error: Undefined function or variable 'SPKpool_path'
Type: ReferenceError

=== Console Output ===
Loading SPKpools from basepath...
Processing Batman...
```

### Syntax Validation

```bash
$ npx ts-node .opencode/skill/matlab-debug/scripts/matlab-cli.ts check Analysis/neurons/08_credit_assignment/analyses/run_credit_assignment.m

=== Syntax Check Results ===
Script: run_credit_assignment.m
Status: VALID
Syntax: OK
Duration: 0.8s
```

### Toolbox Verification

```bash
$ npx ts-node .opencode/skill/matlab-debug/scripts/matlab-cli.ts toolboxes

=== Toolbox Verification ===
✓ Statistics and Machine Learning Toolbox - Available
✓ Signal Processing Toolbox - Available  
✓ Curve Fitting Toolbox - Available
✓ Optimization Toolbox - Available
⚠ Neural Network Toolbox - Not installed (optional for some analyses)
```

## Error Structure

Errors are returned in a structured format that OpenCode can use to identify and fix issues:

```typescript
interface MATLABResult {
  script: string;
  status: 'SUCCESS' | 'ERROR' | 'WARNING';
  duration: number;
  error?: {
    file: string;
    line: number;
    message: string;
    type: string;
    stack: string[];
  };
  output: string[];
  warnings: string[];
  figures: Array<{
    name: string;
    saved_path?: string;
  }>;
  toolbox_status: Record<string, boolean>;
}
```

## Project Integration

The skill automatically detects your project structure and works with any MATLAB organization:

```
Your Project/
├── analyses/               # Main analysis scripts (any name)
├── helpers/               # Helper functions (any name)
├── subfolder/             # Any subdirectory structure
└── *.m                   # Scripts in root directory
```

**Common usage patterns:**

1. **Development workflow**: Write script → `matlab-cli run` → Fix errors → Repeat
2. **Quick validation**: `matlab-cli check` before committing changes  
3. **Debugging complex issues**: `matlab-cli debug` with workspace preservation
4. **Pre-analysis checks**: `matlab-cli toolboxes` to verify dependencies

## Key Features

### Automatic Toolbox Detection
- Scans all `.m` files for toolbox-specific function calls
- Only checks toolboxes actually used in your project
- Supports 8+ major MATLAB toolboxes with expandable detection
- Reports which files use which toolboxes

### Flexible Script Execution
- Works with any MATLAB script structure
- Handles both simple scripts and complex projects
- Preserves original MATLAB working directory
- Manages temporary files automatically

### Figure Handling
- Prevents figure popup during batch execution (configurable)
- Captures figure creation information
- Reports number and names of generated figures
- Optional figure saving for debugging

### Syntax Validation
- Checks MATLAB syntax without executing code
- Identifies parsing errors early in development
- Works with incomplete scripts and code fragments
- Reports specific line and error type

### Error Capture
- Captures full error details with file, line, and stack trace
- Identifies common MATLAB error patterns automatically
- Provides structured output for automated fixing
- Includes warning detection and reporting

## Architecture

```
.opencode/
└── skill/
    └── matlab-debug/
        ├── SKILL.md                          # This file
        ├── router.sh                         # Routes to matlab-cli.ts
        └── scripts/
            └── matlab-cli.ts                 # MATLAB execution engine
```

## Integration with OpenCode

When OpenCode writes MATLAB code, it can automatically:

1. **Test the script** using this skill
2. **Receive structured error information** 
3. **Apply targeted fixes** based on error type and location
4. **Re-test** to validate the fix
5. **Continue development** without your manual intervention

This eliminates the copy-paste error reporting workflow and enables seamless debugging of neural analysis scripts.

---

**MATLAB Debugging Skill** - Instant error capture and debugging for neural analysis scripts!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doublezz10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
