---
name: fal-skill-creator
description: Generate Claude Code Skills from fal.ai model IDs. Fetches OpenAPI schemas from fal.ai Platform API, generates complete skill directories with SKILL.md, wrapper scripts, and queue config. Use when creating new skills for fal.ai models or running fal.ai API jobs with queue management. Use when this capability is needed.
metadata:
  author: el-el-san
---

# fal.ai Skill Creator

Generate reusable Claude Code Skills from fal.ai model IDs using the Platform API.

## Authentication

All fal.ai operations require the `FAL_KEY` environment variable:

```bash
export FAL_KEY="your-fal-api-key"
```

## File Upload (for image/audio/video inputs)

Many models require URL inputs for media files. Use `fal_client` to upload local files:

```bash
# Upload file and get URL
python -c "import fal_client; url=fal_client.upload_file(r'/path/to/file.png'); print(f'URL: {url}')"

# Linux/Mac
python -c "import fal_client; url=fal_client.upload_file('/home/user/image.png'); print(f'URL: {url}')"

# Android (Termux)
python -c "import fal_client; url=fal_client.upload_file('/storage/emulated/0/Download/image.png'); print(f'URL: {url}')"
```

Supported formats: png, jpg, gif, webp, mp3, wav, mp4, webm, etc.

## Quick Start

### Generate Skill from Model ID

```bash
# Generate skill for a model
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --model-id fal-ai/flux/dev

# Lazy mode (minimal SKILL.md, details in YAML)
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --model-id fal-ai/flux/dev --lazy

# Standalone mode (copy shared scripts instead of symlinks)
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --model-id fal-ai/flux/dev --standalone

# Public mode (implies --standalone and validates skill tree has no symlinks)
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --model-id fal-ai/flux/dev --public

# Multiple models
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --model-id fal-ai/flux/dev \
  --model-id fal-ai/flux/schnell
```

Output: `.claude/skills/<model-name>/`

### Search for Models

```bash
python .claude/skills/fal-skill-creator/scripts/generate_fal_skill.py \
  --search "flux" --category "text-to-image"
```

### Direct API Call

```bash
python .claude/skills/fal-skill-creator/scripts/fal_api_call.py \
  --model-id fal-ai/flux/schnell \
  --args '{"prompt": "a cat in a garden"}' \
  --output ./output
```

### Using Generated Skills

After generation, use the wrapper script:

```bash
python .claude/skills/fal-ai-flux-dev/scripts/fal_ai_flux_dev.py \
  --args '{"prompt": "sunset over mountains"}' \
  --output ./output
```

## CLI Reference

### generate_fal_skill.py

| Option | Short | Description |
|--------|-------|-------------|
| `--model-id` | `-m` | fal.ai model ID (can specify multiple) |
| `--search` | `-s` | Search for models by query |
| `--category` | `-c` | Filter search by category |
| `--output` | `-o` | Output directory (default: .claude/skills) |
| `--name` | `-n` | Custom skill name (single model only) |
| `--lazy` | `-l` | Minimal SKILL.md (details in YAML) |
| `--standalone` | | Copy shared scripts into generated skill (no symlink) |
| `--public` | | Public-ready mode (`--standalone` + full skill-tree symlink checks) |
| `--api-key` | `-k` | DEPRECATED; disabled by default (set `FAL_ALLOW_API_KEY_ARG=1` for temporary compatibility) |

### fal_api_call.py

| Option | Short | Description | Default |
|--------|-------|-------------|---------|
| `--model-id` | `-m` | fal.ai model ID (required) | - |
| `--args` | `-a` | Submit arguments (JSON) | - |
| `--args-file` | | Load args from JSON file | - |
| `--api-key` | `-k` | DEPRECATED; disabled by default (set `FAL_ALLOW_API_KEY_ARG=1` for temporary compatibility) | `FAL_KEY` env |
| `--output` | `-o` | Output directory | `./output` |
| `--output-file` | `-O` | Output file path (`--output`配下のみ) | auto |
| `--auto-filename` | | `{request_id}_{timestamp}.{ext}` | off |
| `--poll-interval` | | Status poll interval (seconds) | from config |
| `--max-polls` | | Max poll attempts | computed |
| `--save-logs` | | Save logs to `{output}/logs/` | off |
| `--save-logs-inline` | | Save logs alongside output | off |
| `--no-queue` | | Skip queue daemon only (direct queue submit/status/result flow) | off |
| `--sync-only` | | Use fal.run sync API directly (skip queue submit/poll) | off |

Download size limit: 100MB per file.

### Queue Management

```bash
# Start daemon
python .claude/skills/fal-skill-creator/scripts/queue_daemon.py --background

# Check status
python .claude/skills/fal-skill-creator/scripts/queue_client.py --status

# Shutdown
python .claude/skills/fal-skill-creator/scripts/queue_client.py --shutdown
```

## Generated Skill Structure

```
.claude/skills/<model-name>/
├── SKILL.md
├── queue_config.yaml
├── agents/
│   └── openai.yaml           # UI metadata (display_name / prompt)
├── scripts/
│   ├── fal_api_call.py       # default: symlink / --standalone: copy
│   ├── queue_client.py       # default: symlink / --standalone: copy
│   ├── queue_protocol.py     # default: symlink / --standalone: copy
│   ├── queue_daemon.py       # default: symlink / --standalone: copy
│   └── <model_name>.py       # Wrapper with MODEL_ID preset
└── references/
    ├── model_config.json      # {model_id, name, category, description}
    └── tools/                 # lazy mode only
        └── <model-name>.yaml  # Input/output schema + usage examples
```

## fal.ai Queue API Flow

```
1. SUBMIT    → POST queue.fal.run/{model_id}     → Get request_id
2. STATUS    → GET .../requests/{id}/status       → Wait for COMPLETED
3. RESULT    → GET .../requests/{id}              → Get output URLs
4. DOWNLOAD  → Save files locally
```

Status values: `IN_QUEUE`, `IN_PROGRESS`, `COMPLETED`, `FAILED`

Recovery behavior:
- If submit returns timeout/network error or HTTP 405, attempt request recovery via Platform API (`/v1/models/requests/by-endpoint`) and continue with recovered `request_id`.
- If queue result endpoints return 405 after submit, do not auto-fallback to sync (duplicate billing risk). Return metadata-only result with `status=RESULT_ENDPOINT_ONLY` and `platform_request` when possible.

## Queue Daemon Configuration (queue_config.yaml)

| Setting | Default | Description |
|---------|---------|-------------|
| `max_concurrent` | 2 | Max simultaneous jobs |
| `start_interval` | 1.0 | Seconds between job starts |
| `poll_interval` | 5.0 | Seconds between status polls |
| `job_timeout` | 600 | Job timeout (10 min) |
| `global_rate_per_min` | 10 | Global rate limit |
| `global_burst` | 5 | Burst allowance |
| `model_rates` | {} | Per-model rate overrides |
| `client_idle_timeout` | 0 | Client idle timeout in seconds (0 = disabled) |
| `sync_only` | true | Keep queue manager enabled but execute each queued job via fal.run sync API (set false to use queue submit/status/result in worker) |

Environment variable overrides: `FAL_QUEUE_MAX_CONCURRENT`, `FAL_QUEUE_RATE_PER_MIN`, `FAL_QUEUE_CLIENT_IDLE_TIMEOUT`, etc.

Security: queue daemon enforces runtime dir ownership/permissions (`0700`) and only removes stale socket paths when the existing path is a socket/symlink.

Runtime scope:
- Generated wrappers isolate runtime dirs per skill/model (`~/.cache/fal-queue/<skill>__<model>__v2`), so concurrency/rate limits apply per skill-model daemon.
- Queue client restarts stale daemons when local scripts/config are newer than the daemon start marker (PID file mtime), reducing mixed old/new behavior risks.

## Programmatic Usage

```python
import sys, os
sys.path.insert(0, os.path.join(".claude", "skills", "fal-skill-creator", "scripts"))
from fal_api_call import run_fal_api_job

result = run_fal_api_job(
    model_id="fal-ai/flux/schnell",
    submit_args={"prompt": "a cat in a garden"},
    output_dir="./output",
)
print(result.get("saved_path") or result)
```

## Error Handling

- API errors (4xx/5xx) with automatic retry on transient errors (429, 5xx)
- Job failures (`FAILED` status) with descriptive messages
- Timeout after max polls
- Download failures
- Queue daemon connection failures with fallback to direct API
- On submit timeout/network/405, recover `request_id` from Platform API before deciding fallback
- If queue submit succeeds but queue endpoints return 405, sync fallback is blocked to prevent duplicate execution/billing
- If queue result endpoints remain unavailable, return `RESULT_ENDPOINT_ONLY` with `platform_request` metadata (no automatic retry)

All errors raise exceptions with descriptive messages.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/el-el-san) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
