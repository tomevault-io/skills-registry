---
name: figma-sync
description: | Use when this capability is needed.
metadata:
  author: openclaw
---

# figma-sync

Bidirectional Figma ↔ Code synchronization skill.

## Setup

```bash
export FIGMA_TOKEN="your-personal-access-token"
```

Get a token at https://www.figma.com/developers/api#access-tokens

## Commands

### Pull (Read + Generate Code)

```bash
python3 scripts/figma_pull.py --file-key <KEY> --platform rn-expo --output-dir ./out
python3 scripts/figma_pull.py --file-key <KEY> --node-ids 1:2,3:4 --platform web-react --output-dir ./out
```

Outputs: `designModel.json`, `tokens.json`, `codePlan.json`, and generated component files.

### Push (Write Back)

```bash
python3 scripts/figma_push.py --file-key <KEY> --patch-spec patch.json
python3 scripts/figma_push.py --file-key <KEY> --patch-spec patch.json --execute  # actually apply
```

Dry-run by default. Pass `--execute` to apply changes.

### Diff

```bash
python3 scripts/figma_diff.py --file-key <KEY> --local-model designModel.json
```

Outputs changes and a patchSpec to sync.

### Preview

```bash
python3 scripts/figma_preview.py --file-key <KEY> --operations ops.json
```

Shows what would change without touching anything.

## Platforms

- **rn-expo**: React Native + Expo + TypeScript (primary)
- **web-react**: React + Tailwind CSS (secondary)

## Rate Limits

Uses exponential backoff, ETag caching, and respects Figma's rate limits (~30 req/min).
Cache stored in `.figma-cache/` directory.

## References

- [DesignSpec Schema](references/design-spec-schema.json)
- [API Guide](references/api-guide.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
