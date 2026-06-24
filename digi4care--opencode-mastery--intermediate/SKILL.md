---
name: intermediate-skill
description: Demonstrates helper script integration with sequential execution Use when this capability is needed.
metadata:
  author: digi4care
---

# Intermediate Skill Example

## What I Do

I demonstrate how OpenCode skills integrate with helper scripts for sequential task execution.

## Key Concept

**Skills + Scripts = Workflows.** While the SKILL.md file provides instructions to the AI assistant, helper scripts do the actual work with structured execution, console output, and step-by-step processing.

## Structure

This skill contains two files:

```
skills/intermediate/
├── SKILL.md          ← You are here! (instructions)
└── helper.sh         ← Helper script (execution)
```

The pattern:

1. **SKILL.md** tells the assistant how to use the helper script
2. **helper.sh** contains the actual executable logic
3. **Assistant** executes the script via the Bash tool

## How to Use Me

Execute the helper script to see sequential execution with console output:

```bash
bash src/skill/opencode-mastery/examples/skills/intermediate/helper.sh
```

The script will:

1. Display a header with execution info
2. Execute 5 sequential steps
3. Show step-by-step progress
4. Display a completion message

## What This Demonstrates

### 1. Helper Script Integration

Skills can reference external scripts for execution:

````markdown
## How to Use Me

Execute the helper script:

```bash
bash path/to/helper.sh
```
````

````

The AI assistant reads this instruction and executes the script via the Bash tool.

### 2. Sequential Execution Pattern
The helper demonstrates ordered execution:
```bash
Step 1: Initializing...
Step 2: Processing data...
Step 3: Generating output...
Step 4: Validating results...
Step 5: Finalizing...
````

Each step completes before the next begins - sequential execution.

### 3. Console Output

Rich console feedback for visibility:

- Headers and separators
- Step numbers with timestamps
- Status indicators
- Final completion message

### 4. Script Structure

Reusable pattern for multi-step workflows:

- Header with title
- Sequential numbered steps
- Sleep delays for readability
- Success/failure indicators

## Helper Script Pattern

The `helper.sh` follows this structure:

```bash
#!/bin/bash

# Header
echo "====================================="
echo "  Sequential Execution Example"
echo "====================================="

# Steps (sequential execution)
for step in {1..5}; do
  echo "Step $step: Executing task..."
  # ... do work ...
  echo "  ✓ Completed"
done

# Footer
echo "====================================="
echo "  Execution Complete"
echo "====================================="
```

## When to Use Helper Scripts

Use helper scripts when you need:

- ✅ **Sequential execution** - Multiple steps in order
- ✅ **Console output** - Rich feedback for the user
- ✅ **Validation** - Check conditions before proceeding
- ✅ **Complex logic** - More than simple bash commands
- ✅ **Reusable patterns** - Script used by multiple skills

## Learning Objectives

After studying this intermediate example, you should understand:

1. How to integrate helper scripts with skills
2. The sequential execution pattern
3. How to provide rich console output
4. When to use scripts vs. inline bash commands
5. Basic script structure for multi-step workflows

## Next Steps

- Try the **advanced example** to see full workflow orchestration with config
- Read the **workflow patterns guide** to understand conditional execution
- Check the **best practices guide** for script organization patterns

---

**Difficulty**: ⭐⭐ (Beginner to intermediate)
**Complexity**: Low (2 files: SKILL.md + helper.sh)
**Learning Curve**: Gentle - adds helper script concept
**Use Case**: Multi-step tasks, console output, sequential processing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/digi4care) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
