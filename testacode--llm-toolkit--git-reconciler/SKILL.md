---
name: git-reconciler
description: Sincroniza un branch de trabajo con main/master de forma segura, detectando conflictos antes de mergear. Este skill se usa cuando el usuario dice "sync con main", "actualizar branch", "traer cambios de main", "reconciliar", "merge main", "hay conflictos?", "rebase sobre main", o cuando un branch esta desactualizado respecto a origin/main. Tambien aplica antes de crear un PR para verificar que no haya conflictos pendientes. Use when this capability is needed.
metadata:
  author: testacode
---

# Git Reconciler

Skill para sincronizar tu branch de trabajo con main/master de forma segura, detectando conflictos antes de mergear.

## Proceso

### Paso 1: Verificar estado actual

```bash
git status
git branch --show-current
git status --porcelain
```

**IMPORTANTE**: Si hay cambios sin commitear, preguntar al usuario:
- Queres hacer commit primero?
- Queres hacer stash?
- Queres descartar cambios?

### Paso 2: Identificar branch principal

```bash
# Detectar si es main o master (local, sin network call)
git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@'
```

Si falla (HEAD no seteado):
```bash
git branch -r | grep -E "origin/(main|master)$" | head -1 | sed 's/.*origin\///'
```

### Paso 3: Fetch de cambios remotos

```bash
git fetch origin main  # o master segun detectado
```

### Paso 4: Analizar divergencia

```bash
# Commits en main que no estan en tu branch
git log HEAD..origin/main --oneline

# Commits en tu branch que no estan en main
git log origin/main..HEAD --oneline

# Ver cuanto divergieron
git rev-list --left-right --count origin/main...HEAD
```

El output de `rev-list` es: `<commits_en_main> <commits_en_branch>`

Interpretar:
- `0 0` = Sincronizados, no hay nada que hacer
- `5 0` = Main tiene 5 commits nuevos, tu branch esta atras
- `0 3` = Tu branch tiene 3 commits, main no tiene nuevos (solo push needed)
- `5 3` = Divergencia: main tiene 5 nuevos, tu branch tiene 3

### Paso 5: Detectar conflictos potenciales

```bash
# Archivos modificados en main desde que divergieron
git diff --name-only HEAD...origin/main

# Archivos modificados en tu branch
git diff --name-only origin/main...HEAD
```

Comparar las listas. Si hay archivos en comun, hay riesgo de conflicto.

Para verificar conflictos sin mergear (Git 2.38+):
```bash
git merge-tree --write-tree HEAD origin/main
```

Exit code no-cero indica conflictos. Alternativa legacy:
```bash
git merge-tree $(git merge-base HEAD origin/main) HEAD origin/main
```

Si el output contiene `<<<<<<` o `>>>>>>`, habra conflictos.

### Paso 6: Sugerir estrategia

Presentar al usuario:

**Opcion A: Merge (recomendado para branches compartidos)**
```
git merge origin/main
```
- Preserva historial completo
- Crea commit de merge
- Mas seguro para branches con colaboradores

**Opcion B: Rebase (recomendado para branches personales)**
```
git rebase origin/main
```
- Historial lineal
- Reescribe commits (requiere force push si ya pusheaste)
- Mejor para branches limpios antes de PR

Pregunta:
```
Estrategia recomendada: [merge/rebase] porque [razon].
Cual preferis? [merge/rebase]
```

### Paso 7: Ejecutar sincronizacion

**Si eligio MERGE:**
```bash
git merge origin/main
```

**Si eligio REBASE:**
```bash
git rebase origin/main
```

### Paso 8: Manejar conflictos (si ocurren)

Si hay conflictos, listarlos:
```bash
git diff --name-only --diff-filter=U
```

Mostrar al usuario:
```
Hay conflictos en los siguientes archivos:
- <archivo1>
- <archivo2>

Opciones:
1. Resolver conflictos manualmente (te guio archivo por archivo)
2. Abortar merge: git merge --abort
3. Abortar rebase: git rebase --abort
```

Para cada archivo con conflicto:
```bash
git diff <archivo>
```

### Paso 9: Verificar resultado

```bash
git status
git log --oneline -5
```

---

## Soporte para Worktrees

Si el usuario trabaja con worktrees (patron `.worktrees/`):

### Detectar si estamos en worktree
```bash
git rev-parse --is-inside-work-tree
git worktree list
```

### Consideraciones especiales para worktrees

1. **El main/master suele estar en el repo principal**, no en el worktree
2. **Fetch desde el worktree** funciona igual
3. **Rebase puede ser problematico** si el worktree comparte objetos

Flujo para worktree:
```bash
pwd  # Verificar que estamos en .worktrees/<branch>
git fetch origin main
git merge origin/main
```

---

Para ejemplos detallados, leer `references/examples.md`.
Para tabla de comandos rapida, leer `references/quick-ref.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testacode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
