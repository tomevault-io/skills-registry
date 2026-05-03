---
name: praescientia
description: State checkpointing, divergence detection, and confidence tracking for Hopper/Hardin doctrine compliance. Use PROACTIVELY when performing multi-step reasoning, external actions, or any task requiring error recovery capability. Provides checkpoint_create, checkpoint_list, checkpoint_taint, and confidence_calculate tools. Use when this capability is needed.
metadata:
  author: cddigi
---

# Praescientia: State Management for Doctrine-Compliant Agents

This skill provides the foundational state management tools required by the Hopper and Hardin doctrines. It enables O(1) divergence identification, immutable checkpointing, and confidence tracking.

## Quick Start

Create a checkpoint before any significant action:

```bash
python scripts/checkpoint.py create --task "Analyzing market data" --confidence 0.85
```

List checkpoint chain:

```bash
python scripts/checkpoint.py list
```

Taint a checkpoint chain when divergence is detected:

```bash
python scripts/checkpoint.py taint --from cp_abc123 --reason "Stale price data detected"
```

Calculate confidence decay:

```bash
python scripts/checkpoint.py confidence --initial 0.9 --age-minutes 30 --decay-rate 0.1
```

## Core Tools

### checkpoint_create

Creates an immutable checkpoint of current state.

```bash
python scripts/checkpoint.py create \
  --task "Description of current task" \
  --confidence 0.85 \
  --assumptions "Assumption 1" "Assumption 2" \
  --external-state '{"predictions": [], "positions": []}'
```

Returns: `{"checkpoint_id": "sha256...", "sequence": N, "parent": "..."}`

### checkpoint_list

Lists checkpoint chain with optional filtering.

```bash
# Show all checkpoints
python scripts/checkpoint.py list

# Show only tainted checkpoints
python scripts/checkpoint.py list --tainted-only

# Show last N checkpoints
python scripts/checkpoint.py list --last 5

# Output as JSON for programmatic use
python scripts/checkpoint.py list --json
```

### checkpoint_taint

Marks a checkpoint and all descendants as tainted (Hopper Divergence Protocol).

```bash
python scripts/checkpoint.py taint \
  --from cp_abc123 \
  --reason "Validation failure: stale data"
```

**Important**: Tainted checkpoints are NEVER deleted. They are forensic evidence.

### checkpoint_get

Retrieves a specific checkpoint by ID.

```bash
python scripts/checkpoint.py get cp_abc123
```

### checkpoint_find_valid

Finds the most recent non-tainted checkpoint (recovery point).

```bash
python scripts/checkpoint.py find-valid
```

### confidence_calculate

Calculates confidence with decay for time-sensitive information.

```bash
# Calculate decayed confidence
python scripts/checkpoint.py confidence \
  --initial 0.9 \
  --age-minutes 30 \
  --decay-rate 0.1

# Check if confidence meets threshold
python scripts/checkpoint.py confidence \
  --initial 0.9 \
  --age-minutes 30 \
  --decay-rate 0.1 \
  --threshold 0.7
```

Decay constants by domain:
- Market prices: `--decay-rate 0.1` (per minute)
- News events: `--decay-rate 0.01` (per hour, use `--age-hours`)
- Historical facts: `--decay-rate 0.0001` (per day, use `--age-days`)

### confidence_propagate

Calculates joint confidence for chained reasoning.

```bash
# Chain of 5 steps, each with 0.9 confidence
python scripts/checkpoint.py confidence-chain 0.9 0.9 0.9 0.9 0.9
# Returns: 0.59049

# Or from a file with one confidence per line
python scripts/checkpoint.py confidence-chain --file reasoning_steps.txt
```

## Integration with Doctrines

### Hopper Doctrine Usage

The Hopper Doctrine agent should:

1. **Before any action**: `checkpoint create`
2. **After tool calls**: `checkpoint create` with updated state
3. **On validation failure**: `checkpoint taint` + `checkpoint find-valid`
4. **Before external actions**: Verify `confidence >= 0.7`
5. **Before irreversible actions**: Verify `confidence >= 0.85`

### Hardin Doctrine Usage

The Hardin Doctrine agent should:

1. **Before timing decisions**: Check confidence decay on time-sensitive data
2. **When modeling wait value**: Use confidence calculations for information gain estimates
3. **For adversarial modeling**: Checkpoint strategic positions before analysis

## Checkpoint Storage

Checkpoints are stored in `.praescientia/checkpoints/` as JSON files:

```
.praescientia/
├── checkpoints/
│   ├── cp_abc123.json
│   ├── cp_def456.json
│   └── ...
├── chain.json          # Checkpoint chain index
└── taint_log.json      # Taint history for forensics
```

## State File Format

Each checkpoint file contains:

```json
{
  "checkpoint_id": "sha256 hash",
  "timestamp": "ISO-8601",
  "sequence_number": 1,
  "parent_checkpoint": null,
  "state": {
    "task_description": "string",
    "reasoning_chain": ["step1", "step2"],
    "confidence_score": 0.85,
    "assumptions": ["assumption1"]
  },
  "external_state": {
    "predictions_made": [],
    "irreversible_actions": []
  },
  "tainted": false,
  "taint_reason": null,
  "taint_timestamp": null
}
```

## Error Handling

All commands return structured JSON with success/error status:

```json
{"success": true, "checkpoint_id": "cp_abc123", ...}
{"success": false, "error": "Checkpoint not found: cp_xyz789"}
```

Exit codes:
- 0: Success
- 1: Error (details in stderr and JSON output)

## Requirements

- Python 3.10+
- No external dependencies (stdlib only)

## Version History

- v1.0.0 (2025-01-09): Initial release with checkpoint and confidence tools

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cddigi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
