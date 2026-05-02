---
name: commit-commands
description: Maak commits met gestandaardiseerde berichten (feat, fix, docs, etc) Use when this capability is needed.
metadata:
  author: gertvanhardeveld
---

# Commit Commands Skill

Maak een commit met dit bericht: $ARGUMENTS

## Ondersteunde types:
- **feat**: Nieuwe feature
- **fix**: Bug fix
- **docs**: Documentatie
- **style**: Code style (geen functionele verandering)
- **refactor**: Code refactoring
- **test**: Tests toevoegen/aanpassen
- **chore**: Onderhouds taken

## Voorbeeld gebruik:
```
/commit-commands feat: add user authentication system
/commit-commands fix: resolve login error
/commit-commands docs: update README
```

Volg de conventional commits standaard voor duidelijke en consistente commit berichten.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gertvanhardeveld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
