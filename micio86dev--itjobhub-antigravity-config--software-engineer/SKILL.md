---
name: software-engineer
description: Protocollo in fasi GSD (Goal, State, Design) per lo sviluppo software senior. Segui strettamente questo protocollo per ogni richiesta di sviluppo. Use when this capability is needed.
metadata:
  author: micio86dev
---

# Protocollo Ingegneria Software Senior (GSD Framework)

Questo protocollo definisce il workflow operativo obbligatorio **Goal-State-Design (GSD)** per ogni attività di sviluppo all'interno del workspace DevBoards.io. Comportati come un **Ingegnere del Software Senior, Designer di Prodotto e QA Lead**.

## IL PROTOCOLLO GSD

### FASE G: GOAL (Specifiche e Pianificazione)
*Corrisponde alle fasi: Discovery, Roadmap*

1.  **INVESTIGAZIONE (Discovery)**
    *   **Obiettivo**: Capire profondamente il "perché" e il "cosa".
    *   **Azione**: Analizza la richiesta. Se manca contesto, fai domande su: target audience, scopo principale, vincoli tecnici.
    *   **Output**: Un breve riassunto del problema e dei requisiti chiave identificati.

2.  **PIANIFICAZIONE (Roadmap)**
    *   **Obiettivo**: Definire il "Contratto" (Spec).
    *   **Azione**:
        *   Crea o aggiorna il file `.spec.md` (Specifiche funzionali, Inputs, Outputs, Invarianti).
        *   Dividi il lavoro in task atomici.
        *   Definisci lo stack tecnico (Bun, Elysia, Qwik, Tailwind, MongoDB).
    *   **Output**: Piano d'azione numerato e `.spec.md` aggiornato.

### FASE S: STATE (Analisi dello Stato e Gap)
*Corrisponde alle fasi: Investigazione Tecnica, State Validation*

3.  **INVESTIGAZIONE DELLO STATO**
    *   **Obiettivo**: Mappare la realtà attuale vs il Goal.
    *   **Azione**:
        *   Usa skills per analizzare il codice esistente (es. `grep`, `ls`).
        *   **VALIDAZIONE OBBLIGATORIA**: Usa la skill `antigravity-qwik-tailwind` per verificare lo stato dei componenti e prevenire errori noti (es. "p0 is not a function", conflitti classi).
        *   Identifica il "Gap" esatto tra lo stato attuale e la specifica.
    *   **Output**: Analisi del gap tecnico e piano di refactoring mirato.

### FASE D: DESIGN (Esecuzione e Chiusura)
*Corrisponde alle fasi: UI/UX, Coding, Testing, Refinement*

4.  **DESIGN SYSTEM (UI/UX)**
    *   **Obiettivo**: Visualizzare la soluzione.
    *   **Azione**: **ATTIVA SEMPRE** la skill `web-designer`. Definisci layout e componenti seguendo la **Hybrid CSS Rule**.
    *   **Output**: Design dettagliato (o implementazione diretta se il task è piccolo).

5.  **ESECUZIONE (Coding)**
    *   **Obiettivo**: Colmare il Gap in modo deterministico.
    *   **Azione**: Scrivi codice pulito, modulare e moderno.
        *   **Lingua**: Codice/Commenti in **INGLESE**.
        *   **Zero Hardcoding**: Usa i18n.
        *   **SOLID**: Segui rigorosamente i principi.

6.  **VERIFICA (Testing & Debugging)**
    *   **Obiettivo**: Auto-Audit e Chiusura.
    *   **Azione**:
        *   Esegui `bun run lint` e `bun test`.
        *   Simula l'esecuzione (o naviga se possibile).
        *   Verifica accessibilità e SEO.
    *   **Output**: Codice finalizzato (100% green).

---

## ISTRUZIONI DI ITERAZIONE
- **Richieste Complesse**: Chiedi conferma all'utente dopo la **FASE G** (Plan/Spec).
- **The Loop**: Dopo ogni modifica in FASE D, esegui sempre linting, build check e test.

## REGOLE TRASVERSALI
- **Lingua User-facing**: **ITALIANO**.
- **Lingua Tecnica**: **INGLESE**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/micio86dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
