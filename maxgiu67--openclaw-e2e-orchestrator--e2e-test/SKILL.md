---
name: e2e-test
description: Esegue test E2E reali usando Chrome browser automation Use when this capability is needed.
metadata:
  author: maxgiu67
---

# Skill: Test E2E con Browser Reale

## Scopo

Eseguire test End-to-End su applicazioni web usando Claude Code con accesso diretto a Chrome tramite l'estensione "Claude in Chrome".

## Quando usarla

- Dopo aver rilevato trigger "fai test" su Git
- Per verificare funzionalità web (login, form, navigazione)
- Per test di regressione UI

## Prerequisiti

1. Chrome aperto con estensione "Claude in Chrome" attiva
2. Claude Code connesso all'estensione
3. Servizi target attivi (frontend + backend)
4. Specifiche E2E in `.claude/e2e-tests/`

## Tool browser disponibili

| Tool | Uso |
|------|-----|
| `tabs_context_mcp` | Ottieni tab disponibili |
| `tabs_create_mcp` | Crea nuovo tab |
| `navigate` | Vai a URL |
| `read_page` | Leggi DOM (accessibility tree) |
| `find` | Trova elementi per descrizione |
| `computer` | Click, scroll, screenshot, digita |
| `form_input` | Compila campi form |
| `javascript_tool` | Esegui JS nella pagina |
| `get_page_text` | Estrai testo dalla pagina |

## Procedura Test E2E

### 1. Setup iniziale

```
1. Chiama tabs_context_mcp per ottenere contesto browser
2. Crea nuovo tab con tabs_create_mcp
3. Salva tabId per uso successivo
```

### 2. Per ogni specifica E2E

```
1. Leggi file .claude/e2e-tests/E2E-NNN.md
2. Estrai: URL, steps, verifiche attese

3. Per ogni step:
   a. Esegui azione (navigate, click, fill, etc.)
   b. Attendi caricamento (wait)
   c. Verifica risultato atteso
   d. Se errore → screenshot + log

4. Registra risultato: PASS o FAIL
```

### 3. Navigazione

```javascript
// Vai a una pagina
mcp__claude-in-chrome__navigate({
  url: "http://localhost:8081/login",
  tabId: TAB_ID
})
```

### 4. Trovare elementi

```javascript
// Trova per descrizione naturale
mcp__claude-in-chrome__find({
  query: "campo email",
  tabId: TAB_ID
})

// Leggi struttura pagina
mcp__claude-in-chrome__read_page({
  tabId: TAB_ID,
  filter: "interactive"  // solo elementi interattivi
})
```

### 5. Interazioni

```javascript
// Click su elemento
mcp__claude-in-chrome__computer({
  action: "left_click",
  coordinate: [x, y],
  tabId: TAB_ID
})

// Oppure click su ref
mcp__claude-in-chrome__computer({
  action: "left_click",
  ref: "ref_123",
  tabId: TAB_ID
})

// Digitare testo
mcp__claude-in-chrome__computer({
  action: "type",
  text: "test@example.com",
  tabId: TAB_ID
})

// Compilare form input
mcp__claude-in-chrome__form_input({
  ref: "ref_456",
  value: "password123",
  tabId: TAB_ID
})
```

### 6. Screenshot

```javascript
// Cattura schermata
mcp__claude-in-chrome__computer({
  action: "screenshot",
  tabId: TAB_ID
})
```

### 7. Verifiche

```javascript
// Leggi testo pagina
mcp__claude-in-chrome__get_page_text({
  tabId: TAB_ID
})

// Verifica elemento presente
mcp__claude-in-chrome__find({
  query: "messaggio Benvenuto",
  tabId: TAB_ID
})

// Esegui JS per verifiche
mcp__claude-in-chrome__javascript_tool({
  action: "javascript_exec",
  text: "document.querySelector('.success') !== null",
  tabId: TAB_ID
})
```

## Pattern per test comuni

### Login

```
1. navigate → pagina login
2. find → campo telefono/email
3. form_input → inserisci credenziali
4. find → bottone "Invia codice" o "Login"
5. click → sul bottone
6. wait → 3 secondi
7. find → campo OTP (se presente)
8. form_input → inserisci OTP
9. click → bottone "Verifica"
10. wait → caricamento dashboard
11. verifica → testo "Dashboard" o "Benvenuto"
```

### Compilazione form

```
1. navigate → pagina form
2. read_page filter=interactive → lista campi
3. Per ogni campo:
   - form_input → valore test
4. find → bottone submit
5. click → submit
6. wait → risposta
7. verifica → messaggio successo
```

### Navigazione menu

```
1. find → menu item
2. click → menu item
3. wait → caricamento
4. verifica → URL o contenuto pagina
```

## Gestione errori

### Elemento non trovato

```
1. screenshot → stato attuale
2. read_page → analizza DOM
3. Prova selettori alternativi
4. Se ancora non trovato → FAIL con dettaglio
```

### Timeout

```
1. screenshot → stato attuale
2. Verifica connettività servizi
3. Aumenta wait e riprova (1 volta)
4. Se ancora timeout → FAIL
```

### Errore applicazione

```
1. screenshot → errore visibile
2. Leggi console (read_console_messages)
3. Leggi network (read_network_requests)
4. Documenta errore per fix
```

## Output

Per ogni test eseguito, scrivi:

```markdown
## Test: E2E-NNN - [Titolo]

**Risultato**: PASS | FAIL
**Durata**: X secondi
**Iterazione**: N

### Steps eseguiti
1. [step] → OK
2. [step] → OK
3. [step] → FAIL: [motivo]

### Screenshot
- `screenshots/E2E-NNN-step3-fail.png`

### Errore (se FAIL)
[Descrizione dettagliata]

### Fix suggerito (se FAIL)
[Cosa modificare per risolvere]
```

## Sicurezza

- Non inserire credenziali reali di produzione
- Usa solo ambienti di test/staging
- Non eseguire azioni distruttive (delete account, etc.)
- Screenshot potrebbero contenere dati sensibili → non condividere

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxgiu67) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
