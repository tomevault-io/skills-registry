---
name: babysit
description: Orchestrate via @babysitter. Use this skill when asked to babysit a run, orchestrate a process or whenever it is called explicitly. (babysit, babysitter, orchestrate, orchestrate a run, workflow, etc.) Use when this capability is needed.
metadata:
  author: a5c-ai
---

# babysit

Orchestrate `.a5c/runs/<runId>/` through iterative execution.

## Dependencies

### Babysitter SDK and CLI

Read the SDK version from `versions.json` to ensure version compatibility:

```bash
SDK_VERSION=$(node -e "try{console.log(JSON.parse(require('fs').readFileSync('${CLAUDE_PLUGIN_ROOT}/versions.json','utf8')).sdkVersion||'latest')}catch{console.log('latest')}")
sudo npm i -g @a5c-ai/babysitter-sdk@$SDK_VERSION
# sudo is depending on the env and system
```

then use the CLI alias: CLI="babysitter"

**Alternatively, use the CLI alias:** `CLI="npx -y @a5c-ai/babysitter-sdk@$SDK_VERSION"`

### jq

make sure you have jq installed and available in the path. if not, install it.

## Instructions

Run the following command to get full orchestration instructions:

```bash
babysitter instructions:babysit-skill --harness claude-code --interactive
```

For non-interactive mode (running with `-p` flag or no AskUserQuestion tool):

```bash
babysitter instructions:babysit-skill --harness claude-code --no-interactive
```

Follow the instructions returned by the command above to orchestrate the run.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/a5c-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
