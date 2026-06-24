---
name: vibe-coding
description: | Use when this capability is needed.
metadata:
  author: Schero94
---

# Vibe Coding Best Practices

Dieser Skill enthält Meta-Strategien für effektives AI-Pair-Programming.

## Prinzipien

### 1. Context7 First

**VOR jeder größeren Implementierung** aktuelle Docs holen:

```
1. Context7: resolve-library-id → "library-name"
2. Context7: query-docs → "spezifische Frage"
3. DANN implementieren
```

### 2. Token-Budget Management

**~200k Tokens effektiver Arbeitskontext** — damit haushalten!

- **Kleine Dateien**: Max 200-300 Zeilen (ideal <150)
- **Fokussierte Reads**: Nur relevante Bereiche lesen
- **Minimale Diffs**: Nur ändern was nötig ist

Bei Limit-Annäherung:
1. **Stopp** — nicht weiter Kontext aufbauen
2. **Zusammenfassen** — Entscheidungen, Invarianten, TODOs
3. **Weiterarbeiten** von der Zusammenfassung aus

### 3. Workflow-Driven Development

Nutze `/workflow-name` für wiederkehrende Tasks:

| Workflow | Wann |
|----------|------|
| `/add-primitive` | Neuer Contract Wrapper |
| `/add-ai-provider` | Neuer AI Provider |
| `/add-cli-command` | Neuer CLI Command |
| `/test-contract` | Contract Testing |
| `/debug-tx` | TX Debugging |
| `/run-tests` | Test Execution |
| `/build-release` | Release |
| `/context7-lookup` | Docs abrufen |

### 4. Kommunikation

**Klar und präzise kommunizieren:**

- **Was** du erreichen willst
- **Warum** (Kontext hilft!)
- **Constraints** (Zeit, Kompatibilität, etc.)

**Beispiel:**
> "Füge Batch-Burn Support hinzu. Soll bis zu 10 Burns in einer TX bündeln für Gas-Effizienz. Muss backward-compatible sein."

### 5. Inkrementell arbeiten

**Kleine, testbare Schritte:**

1. Types definieren
2. Interface implementieren
3. Einen Test schreiben
4. Implementierung
5. Weitere Tests
6. Docs

**NICHT:** Alles auf einmal, dann debuggen.

## Code Quality Checks

### Vor Commit

```bash
npm run typecheck  # TypeScript OK?
npm test          # Tests grün?
npm run lint      # Lint OK?
```

### Vor PR/Release

```bash
npm run test:coverage  # Coverage OK?
npm run build          # Build OK?
npm run docs           # Docs generiert?
```

## Projekt-spezifische Infos

### Deployed Contracts (Mainnet)

| Contract | Address |
|----------|---------|
| Proof-of-Burn | `terra188fdk...7uq0ch3d0` |
| Negative-Staking | `terra13kz7...qlscqnegaws` |
| Stable-Vault | `terra1g6pyt...vfkapv6q4s7rvj` |

### Key Files

| Was | Wo |
|-----|-----|
| Main SDK | `src/LumosPrimitives.ts` |
| Legacy SDK | `src/LunaSDK.ts` |
| Primitives | `src/primitives/` |
| AI | `src/ai/` |
| CLI | `src/cli/` |
| Tests | `tests/` |

### Commands

```bash
npm run dev          # Watch mode
npm run build        # Build
npm test             # Tests
npm run dashboard    # Vue Dashboard
npm run ai-oracle    # AI Oracle Service
```

## Troubleshooting

### "Out of context"

→ Chat zusammenfassen, neuen Chat starten

### "Types don't match"

→ `npm run typecheck` für Details

### "Test fails mysteriously"

→ `npm run test:watch -- --grep "test-name"` für Isolation

### "Contract call fails"

→ `/debug-tx` Workflow nutzen

## Creating New Skills

Wenn ein Workflow wiederholt gebraucht wird:

1. Erstelle `.windsurf/skills/[name]/SKILL.md`
2. Frontmatter mit `name` und `description`
3. Triggering-Keywords in description
4. Klare, schrittweise Anleitung

```yaml
---
name: my-skill
description: |
  Was der Skill macht. Nutze wenn...
  Trigger-Keywords: "keyword1", "keyword2"
---

# Skill Title

[Instructions...]
```

---
> Source: [Schero94/lumos-luna-sdk](https://github.com/Schero94/lumos-luna-sdk) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
