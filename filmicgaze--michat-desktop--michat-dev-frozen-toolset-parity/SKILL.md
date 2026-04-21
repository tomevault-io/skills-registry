---
name: michat-dev-frozen-toolset-parity
description: Prevent dev vs frozen tool behavior drift in MiChat. Use whenever changing toolset code, tool schemas/metadata, toolset configs, loader behavior, or build/spec wiring that affects tool loading. Use when this capability is needed.
metadata:
  author: filmicgaze
---

# MiChat Dev/Frozen Toolset Parity

Internal toolsets are canonical. Frozen behavior must match for the same profile/toolsets/config.

## Canonical rule

- Treat `michat/toolsets/<id>/__init__.py` as canonical behavior.
- Keep `toolsets/<id>/toolset.py` behavior-equivalent for frozen runtime.
- Treat external config files (for example `toolsets/<id>/config.json`) as runtime contract files.

## Common failure modes

1. Internal/external code drift
- Internal toolset changed; external `toolset.py` not updated.

2. External config drift
- Runtime config in `toolsets/<id>/` no longer matches intended behavior.

3. Loader path mismatch
- Dev path tested (`michat.toolsets.*`) but frozen path (`_load_external_toolset`) not tested.

4. Build/staging mismatch
- Dist contains stale/missing external toolset code or config.

## Required workflow when touching toolsets

### 1) Auto-detect touched toolset IDs

Do not hand-maintain toolset lists.

Use changed files from current working tree vs `HEAD`, then derive IDs from both trees:
- internal pattern: `michat/toolsets/<id>/...`
- external pattern: `toolsets/<id>/...`

If nothing is detected, ask for explicit IDs before proceeding.

```powershell
@'
import re, subprocess

changed = subprocess.run(
    ["git", "diff", "--name-only", "HEAD"],
    capture_output=True, text=True, check=True
).stdout.splitlines()

ids = set()
for p in changed:
    m = re.match(r"michat/toolsets/([^/]+)/", p.replace("\\\\", "/"))
    if m:
        ids.add(m.group(1))
    m = re.match(r"toolsets/([^/]+)/", p.replace("\\\\", "/"))
    if m:
        ids.add(m.group(1))

print(" ".join(sorted(ids)))
'@ | python -
```

### 2) Implement with mirror discipline

- Apply behavior change in internal canonical module.
- Mirror behavior-critical changes into external module.
- Mirror required external config changes (`toolsets/<id>/*.json` etc.).
- Update `toolsets/<id>/TOOLSET.md` if contract text changed.

### 3) Run contract parity checks (required)

Use frozen-style module loading and canonicalized schema comparison.

Parity checks must compare, for each touched toolset:
- `get_tools(...)` contract shape
- `get_handlers(...)` tool name set
- tool metadata key set (`get_tool_metadata` or `TOOL_METADATA`)

Canonicalize before comparing:
- sort dict keys recursively
- preserve list order by default
- sort only known order-insensitive scalar lists (`required`, `enum`)

```powershell
@'
import importlib
import json
import re
import subprocess
import sys
from pathlib import Path

import michat.toolsets as registry
from michat.profile_loader import load_profile
from michat.runtime_paths import get_profiles_dir

ORDER_INSENSITIVE_LIST_KEYS = {"required", "enum"}

def norm(v, key=None):
    if isinstance(v, dict):
        return {k: norm(v[k], k) for k in sorted(v.keys())}
    if isinstance(v, list):
        out = [norm(x, key) for x in v]
        if key in ORDER_INSENSITIVE_LIST_KEYS and all(isinstance(x, (str, int, float, bool, type(None))) for x in out):
            return sorted(out, key=lambda x: json.dumps(x, sort_keys=True))
        return out
    return v

def get_tool_sig(mod, profile, context):
    rows = {}
    for t in (mod.get_tools(profile=profile, context=context) or []):
        if isinstance(t, dict) and isinstance(t.get("name"), str):
            rows[t["name"]] = {
                "type": t.get("type"),
                "parameters": norm(t.get("parameters")),
            }
    return norm(rows)

def get_handler_sig(mod, profile, context):
    h = mod.get_handlers(profile=profile, context=context) or {}
    return sorted([k for k in h.keys() if isinstance(k, str)])

def get_metadata_sig(mod, profile, context):
    meta = None
    gm = getattr(mod, "get_tool_metadata", None)
    if callable(gm):
        try:
            meta = gm(profile=profile, context=context)
        except Exception:
            meta = None
    if meta is None:
        meta = getattr(mod, "TOOL_METADATA", None)
    if not isinstance(meta, dict):
        return []
    return sorted([k for k in meta.keys() if isinstance(k, str)])

def touched_ids():
    changed = subprocess.run(
        ["git", "diff", "--name-only", "HEAD"],
        capture_output=True, text=True, check=True
    ).stdout.splitlines()
    ids = set()
    for p in changed:
        p = p.replace("\\\\", "/")
        m = re.match(r"michat/toolsets/([^/]+)/", p)
        if m:
            ids.add(m.group(1))
        m = re.match(r"toolsets/([^/]+)/", p)
        if m:
            ids.add(m.group(1))
    return sorted(ids)

ids = touched_ids()
if not ids:
    raise SystemExit("No touched toolset IDs detected from git diff --name-only HEAD.")

profile = None
context = {}
assumption = None

try:
    profile = load_profile("assistant")
    profile_dir = Path(profile["profile_dir"])
    workspace_root = profile.get("workspace_root")
    context = {
        "profile_dir": str(profile_dir),
        "skills_root": str(profile_dir / "skills"),
        "global_skills_root": str(get_profiles_dir() / "_global" / "skills"),
        "workspace_root": workspace_root,
        "allow_skill_editing": bool(profile.get("allow_skill_editing", False)),
        "allow_web_browsing": bool(profile.get("allow_web_browsing", False)),
    }
except Exception as exc:
    assumption = f"profile-aware context unavailable; using neutral context only ({exc})"
    profile = None
    context = {}

if assumption:
    print(f"ASSUMPTION: {assumption}")

for name in ids:
    internal = importlib.import_module(f"michat.toolsets.{name}")
    sys.modules.pop(f"michat.toolsets.{name}", None)
    external = registry._load_external_toolset(name)

    if "toolsets" not in str(external.__file__).replace("\\\\", "/"):
        raise AssertionError(f"{name}: external module path unexpected: {external.__file__}")

    assert get_tool_sig(internal, profile, context) == get_tool_sig(external, profile, context), f"{name}: get_tools mismatch"
    assert get_handler_sig(internal, profile, context) == get_handler_sig(external, profile, context), f"{name}: get_handlers mismatch"
    assert get_metadata_sig(internal, profile, context) == get_metadata_sig(external, profile, context), f"{name}: metadata mismatch"
    print(f"parity-ok: {name}")
'@ | python -
```

### 4) Run minimal behavior smoke tests (required)

For each touched toolset, run:
- 1 happy-path call
- 1 expected-rejection call
- in both internal and frozen-style external modules when practical

Use temp directories and patch OS side effects where needed.

### 5) Build and verify dist staging (required for touched toolsets)

Build the target edition:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build_windows.ps1 -Edition public
```

Then verify source vs dist hashes for touched external files:
- always: `toolsets/<id>/toolset.py`
- include external config files under `toolsets/<id>/` (`*.json`, `*.yaml`, `*.yml`)

```powershell
@'
import hashlib
import re
import subprocess
from pathlib import Path

repo = Path(".").resolve()
dist = repo / "dist" / "MiChat" / "toolsets"

changed = subprocess.run(
    ["git", "diff", "--name-only", "HEAD"],
    capture_output=True, text=True, check=True
).stdout.splitlines()

ids = set()
for p in changed:
    p = p.replace("\\\\", "/")
    m = re.match(r"michat/toolsets/([^/]+)/", p)
    if m:
        ids.add(m.group(1))
    m = re.match(r"toolsets/([^/]+)/", p)
    if m:
        ids.add(m.group(1))

def sha256(path: Path) -> str:
    h = hashlib.sha256()
    h.update(path.read_bytes())
    return h.hexdigest()

for tid in sorted(ids):
    src_root = repo / "toolsets" / tid
    dst_root = dist / tid
    if not src_root.is_dir():
        continue
    for src in [src_root / "toolset.py"] + sorted(src_root.glob("*.json")) + sorted(src_root.glob("*.yaml")) + sorted(src_root.glob("*.yml")):
        if not src.is_file():
            continue
        dst = dst_root / src.name
        if not dst.is_file():
            raise AssertionError(f"missing dist file: {dst}")
        if sha256(src) != sha256(dst):
            raise AssertionError(f"hash mismatch: {src} vs {dst}")
        print(f"staged-ok: {tid}/{src.name}")
'@ | python -
```

## Done gate

Do not declare completion until all are true:
- touched toolset IDs were auto-detected (or explicitly documented fallback)
- internal/external contract parity passed with canonicalized comparison
- profile-aware context was used, or fallback assumption was explicitly logged
- minimal behavior smoke passed
- dist staging/hash verification passed for `toolset.py` plus external config files

## Anti-patterns

- Manually curating touched toolset IDs.
- Testing only dev imports and assuming frozen parity.
- Comparing raw schema dicts without canonicalization.
- Skipping context assumptions in parity runs.
- Verifying only code files and ignoring runtime config files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filmicgaze) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
