---
name: git-monitor
description: Monitora repository Git per trigger 'fai test Use when this capability is needed.
metadata:
  author: maxgiu67
---

# Skill: Monitor Git per Trigger Test

## Scopo

Monitorare un repository Git per rilevare commit con keyword "fai test" e avviare automaticamente il workflow di test E2E.

## Quando usarla

- Nel heartbeat periodico (ogni 30 secondi)
- Per verificare stato repository
- Per gestire branch di test

## Prerequisiti

1. Repository Git configurato in `memory/config.json`
2. Accesso SSH/HTTPS al remote
3. Permessi di lettura sul repository

## Procedura di monitoring

### 1. Carica configurazione

```bash
# Leggi config
cat memory/config.json
```

Estrai:
- `repoPath`: dove si trova il repo
- `remoteName`: nome remote (origin)
- `mainBranch`: branch da monitorare (main)
- `triggerKeyword`: parola chiave ("fai test")

### 2. Fetch aggiornamenti

```bash
cd $REPO_PATH
git fetch $REMOTE $BRANCH 2>&1
```

Se fallisce:
- Verifica connettività
- Verifica credenziali SSH
- Log errore e riprova al prossimo heartbeat

### 3. Leggi ultimo commit

```bash
# Hash commit
COMMIT_HASH=$(git rev-parse $REMOTE/$BRANCH)

# Messaggio commit
COMMIT_MSG=$(git log $REMOTE/$BRANCH --oneline -1 --format="%s")

# Autore e data
COMMIT_INFO=$(git log $REMOTE/$BRANCH -1 --format="%an|%ai")
```

### 4. Controlla trigger

```bash
# Verifica se contiene keyword
echo "$COMMIT_MSG" | grep -i "$TRIGGER_KEYWORD"
```

Se trovato:
1. Leggi `memory/processed-commits.json`
2. Cerca `$COMMIT_HASH` nella lista
3. Se NON presente → **TRIGGER ATTIVATO**
4. Se presente → ignorare (già processato)

### 5. Gestione trigger attivato

```bash
# Crea branch di test
BRANCH_NAME="test-auto-$(date +%Y%m%d-%H%M%S)"
git checkout -b $BRANCH_NAME $REMOTE/$BRANCH

# Salva stato
cat > memory/current-test.json << EOF
{
  "commitHash": "$COMMIT_HASH",
  "commitMessage": "$COMMIT_MSG",
  "startTime": "$(date -Iseconds)",
  "branch": "$BRANCH_NAME",
  "iteration": 0,
  "status": "starting"
}
EOF
```

### 6. Avvia test E2E

Passa il controllo alla skill `e2e-test` con:
- Path repository
- Nome branch test
- Configurazione da config.json

## Gestione commit processati

### Aggiungere commit completato

```bash
# Leggi file esistente
PROCESSED=$(cat memory/processed-commits.json)

# Aggiungi nuovo entry
# (usa tool di scrittura file, non bash)
```

Struttura entry:
```json
{
  "hash": "abc123def",
  "message": "fai test - feature X",
  "processedAt": "2024-01-15T10:30:00Z",
  "result": "ok",
  "iterations": 3,
  "branch": "test-auto-20240115-103000"
}
```

### Pulizia vecchi commit

Mantieni solo ultimi 100 commit processati per evitare file troppo grandi.

## Gestione branch di test

### Dopo successo (tutti test passano)

```bash
# Commit risultati
git add test-results/
git commit -m "ok - tutti i test passati (iter N)"

# Push branch
git push origin $BRANCH_NAME

# Opzionale: merge su main
git checkout main
git merge $BRANCH_NAME
git push origin main

# Cleanup
git branch -d $BRANCH_NAME
```

### Dopo fallimento (timeout o errori)

```bash
# Commit stato finale
git add test-results/
git commit -m "test falliti - N errori dopo M iterazioni"

# Push per analisi
git push origin $BRANCH_NAME

# NON fare merge su main
# Lascia branch per debug
```

## Stati possibili

| Stato | Significato |
|-------|-------------|
| `starting` | Trigger rilevato, setup in corso |
| `running` | Test in esecuzione |
| `fixing` | Applicando fix dopo fallimento |
| `success` | Tutti test passati |
| `failed` | Timeout o errori non risolvibili |
| `aborted` | Interrotto manualmente |

## Comandi Git utili

```bash
# Stato repository
git status

# Log recenti
git log --oneline -10

# Differenze
git diff HEAD~1

# Branch attivi
git branch -a

# Remote info
git remote -v

# Annulla modifiche locali
git checkout -- .

# Reset a remote
git reset --hard $REMOTE/$BRANCH
```

## Errori comuni

### "Permission denied (publickey)"
- Verifica SSH key configurata
- Verifica agent SSH attivo

### "Repository not found"
- Verifica URL remote
- Verifica permessi accesso

### "Merge conflict"
- Non dovrebbe succedere su branch isolato
- Se succede: abort e notifica

## Output atteso

Ogni esecuzione del monitor deve:
1. Loggare azione intrapresa
2. Aggiornare `memory/current-test.json` se test in corso
3. Aggiornare `memory/processed-commits.json` se completato
4. Ritornare stato per heartbeat

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxgiu67) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
