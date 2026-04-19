---
name: git-workflow
description: Flujos de trabajo Git profesionales y mejores prácticas Use when this capability is needed.
metadata:
  author: kaltwulx
---

# Git Workflow

Guía de flujos de trabajo Git para equipos profesionales.

## Flujos Recomendados

### Git Flow (Proyectos con releases)
```
main        ●─────●─────●
             ╲       ╲
develop       ●──●──●──●──●
               ╲      ╲
feature/*        ●──●     ●──●
```

- `main`: Producción estable
- `develop`: Integración continua
- `feature/*`: Features individuales
- `release/*`: Preparación de releases
- `hotfix/*`: Correcciones urgentes

### GitHub Flow (Proyectos ágiles)
```
main    ●────●────●────●
         ╲    ╲    ╲
feature    ●─●   ●─●   ●─●
```

- `main`: Siempre deployable
- Branches de feature cortas
- PRs con code review obligatorio
- Merge squash para historia limpia

### Trunk-Based Development (CI/CD)
- Commits frecuentes a main
- Feature flags para código incompleto
- Branches máximo 1 día de vida
- Pair programming recomendado

## Convenciones de Commits

### Formato Conventional Commits
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: Nueva feature
- `fix`: Bug fix
- `docs`: Documentación
- `style`: Formato (no código)
- `refactor`: Refactorización
- `test`: Tests
- `chore`: Tareas de mantenimiento

**Ejemplos:**
```
feat(auth): add JWT authentication

Implement JWT-based auth with refresh tokens.
Includes login, logout, and token refresh endpoints.

Closes #123
```

## Mejores Prácticas

1. **Commits Atómicos**: Un cambio lógico por commit
2. **Mensajes Descriptivos**: Explica el "porqué", no solo el "qué"
3. **Branches Cortas**: Máximo 2-3 días de vida
4. **Rebase vs Merge**: Usa rebase para historia lineal
5. **Code Review**: Todo código debe ser revisado
6. **Commits Frecuentes**: Commit early, commit often

## Comandos Útiles

### Historia Limpia
```bash
# Rebase interactivo
git rebase -i HEAD~5

# Amend al último commit
git commit --amend --no-edit

# Squash de commits
git reset --soft HEAD~3
git commit -m "mensaje consolidado"
```

### Seguridad
```bash
# Ver cambios antes de push
git diff origin/main

# Stash temporal
git stash push -m "contexto"
git stash pop

# Cherry-pick
git cherry-pick <commit-hash>
```

## Anti-Patrones

❌ **Evitar:**
- Commits con mensajes como "fix", "update", "WIP"
- Branches de meses de antigüedad
- Push force a branches compartidas
- Merge commits innecesarios
- Archivos grandes/binarios en repo

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaltwulx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
