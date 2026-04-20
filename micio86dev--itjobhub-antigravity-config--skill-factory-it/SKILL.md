---
name: skill-factory-it
description: Questa skill permette di creare altre skills nel workspace in lingua italiana, seguendo gli standard di Google Antigravity. Use when this capability is needed.
metadata:
  author: micio86dev
---

# Skill Factory (Italiano)

Questa skill abilita l'agente a generare nuove competenze (skills) per il workspace. Ogni nuova skill deve essere localizzata in italiano per facilitare la collaborazione con l'utente, mantenendo però una struttura tecnica compatibile con i requisiti di sistema.

## Quando usare questa skill

- Quando l'utente richiede una nuova funzionalità specifica che richiede istruzioni dettagliate per l'agente (es. un esperto di SEO, un assistente per i post sui social, un generatore di scraper).
- Quando è necessario automatizzare un processo ripetitivo attraverso istruzioni standardizzate.

## Come creare una nuova skill

Segui questi passaggi per creare una nuova skill:

1. **Definisci il Nome**: Usa un identificativo unico (es. `esperto-seo`).
2. **Crea la Cartella**: `.agent/skills/<identificativo>/`.
3. **Crea il file SKILL.md**: Deve contenere il frontmatter YAML e le istruzioni in italiano.
4. **Struttura consigliata**:
   ```markdown
   ---
   name: <identificativo>
   description: <Breve descrizione della funzione in italiano>
   ---
   # <Titolo della Skill>
   ...istruzioni dettagliate...
   ```
5. **Aggiungi Script (Opzionale)**: Se la skill richiede logica deterministica, crea una cartella `scripts/` e aggiungi file Python o TypeScript.

## Esempio di creazione
Se l'utente dice: "Crea una skill per analizzare il tono di voce dei post", l'agente deve creare `.agent/skills/analizzatore-tono/SKILL.md` con le istruzioni appropriate.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/micio86dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
