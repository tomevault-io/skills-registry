---
name: astra-docker
description: cat <<'EOF' > ~/.openclaw/workspace/skills/astra-docker/SKILL.md Use when this capability is needed.
metadata:
  author: openclaw
---
cat <<'EOF' > ~/.openclaw/workspace/skills/astra-docker/SKILL.md
---
name: astra-docker
description: "Execute commands, read files, and write files in Astra's Docker container workspace (astra-env). Use this skill whenever you need to interact with your virtual environment at /workspace."
---

# Docker Workspace Access

You have a persistent Docker container called `astra-env` with a workspace mounted at `/workspace`.

## How to Use

Use the `bash` tool to run commands inside the container:

### Execute a command
```bash
sudo docker exec -w /workspace astra-env bash -c "YOUR_COMMAND_HERE"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
