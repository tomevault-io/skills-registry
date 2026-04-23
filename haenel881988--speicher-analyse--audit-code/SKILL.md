---
name: audit-code
description: Codequalitäts-Audit für die Speicher Analyse Tauri-App (React + Vite + TypeScript Frontend). Prüft Security, Performance, WCAG-Kontrast, Tauri-Best-Practices und Projektkonventionen. Aufruf mit /audit-code [datei-oder-modul oder "all"]. Use when this capability is needed.
metadata:
  author: haenel881988
---

# Code-Audit

Du führst ein Codequalitäts-Audit für die Speicher Analyse Tauri-App durch.

## Argument

`$ARGUMENTS` = Dateipfad, Modulname ODER `all` für vollständiges Audit

## Audit-Kategorien

### 1. Security (KRITISCH)

- **Command Injection (Rust):** `format!()` für PowerShell ohne `.replace("'", "''")`?
- **XSS (React):** `dangerouslySetInnerHTML` mit ungeprüften Daten? (JSX escaped automatisch)
- **Path Traversal:** Dateipfade vom Frontend ohne Validierung an Dateioperationen?
- **CSP:** `tauri.conf.json` → `security.csp` korrekt (NICHT `null`)?
- **withGlobalTauri:** Ist `true` gesetzt (React-App braucht `window.__TAURI__`)?
- **Capabilities:** Minimal-Prinzip?
- **Parameter-Validierung:** IPC-Parameter validiert/escaped?

### 2. Performance

- **Blockierende Operationen:** `std::fs::*` statt `tokio::fs::*` in Commands?
- **Parallele PowerShell:** PS-Prozesse parallel? (müssen sequenziell sein)
- **Timeouts:** `run_ps()` mit `tokio::time::timeout()`?
- **Chart.js Animationen:** `animation: false`?
- **React Re-Renders:** Unnötige State-Updates? Fehlende `useMemo`/`useCallback`?
- **useEffect Cleanup:** Timer, Listener, Observer korrekt in Cleanup-Return?
- **Lazy Loading:** Views über `React.lazy()` geladen?

### 3. WCAG / Barrierefreiheit

**WICHTIG: WCAG-Prüfung MUSS visuell über `/visual-verify` erfolgen.**

- **Kontrastwerte:** Text-auf-Hintergrund mindestens 4.5:1
- **Akzentfarben:** `--accent` NUR für Borders/Backgrounds, NICHT für Text
- **ARIA-Attribute:** `role`, `aria-label`, `aria-expanded`

### 4. Tauri Best Practices

- **Single Instance:** `tauri-plugin-single-instance` aktiv?
- **Command-Registrierung:** Alle Commands in `lib.rs` → `generate_handler![]`?
- **Stubs:** Commands die nur `Ok(json!({"success": true}))` zurückgeben?
- **Mutex-Handling:** `Mutex`/`RwLock` korrekt?

### 5. Projektkonventionen (React + TypeScript)

- **TypeScript:** Typen korrekt definiert? `any` vermieden?
- **React Patterns:** Funktionale Komponenten, `export default`, hooks
- **API-Aufrufe:** Über `import * as api from '../api/tauri-api'` (nicht direkt `invoke`)
- **Error-Handling:** try/catch mit `showToast` aus AppContext
- **Deutsche UI-Texte:** Korrekte Umlaute (ä, ö, ü, ß)?
- **Kein `dangerouslySetInnerHTML`:** JSX escaped automatisch
- **useEffect Dependencies:** Korrekt angegeben?

### 6. Stub-Erkennung

- **Fake-Funktionen:** Commands die `success: true` oder `stub: true` zurückgeben?
- **Frontend-Warnung:** Erkennt das Frontend Stub-Antworten?

## Audit-Ablauf

### Einzelne Datei (`/audit-code src-tauri/src/commands/cmd_scan.rs`)

1. Datei vollständig lesen
2. Alle 6 Kategorien prüfen
3. Audit-Bericht erstellen

### Ganzes Modul (`/audit-code privacy`)

1. Lies `src-tauri/src/commands/cmd_privacy.rs`
2. Lies `src/views/PrivacyView.tsx`
3. Prüfe API-Funktionen in `src/api/tauri-api.ts`
4. Prüfe relevantes CSS in `src/style.css`
5. Audit-Bericht erstellen

### Vollständiges Audit (`/audit-code all`)

**PFLICHT: ALLE Dateien vollständig lesen. NICHT nur greppen.**

1. **Rust-Backend:** Lies `src-tauri/src/commands/cmd_*.rs`, `ps.rs`, `scan.rs`, `lib.rs`
2. **Konfiguration:** Lies `src-tauri/tauri.conf.json`
3. **Frontend:** Lies ALLE `src/views/*.tsx` Dateien
4. **API-Bridge:** Lies `src/api/tauri-api.ts`
5. **Components:** Lies `src/components/*.tsx`
6. **CSS:** Lies `src/style.css`
7. **Prüfe ALLE 6 Kategorien**

## Ausgabeformat

```markdown
## Code-Audit: [Datei/Modul/Gesamt]

### Zusammenfassung
- Geprüfte Dateien: X
- Gefundene Probleme: X (Y kritisch, Z Hinweise)

### Kritische Probleme
| # | Kategorie | Datei:Zeile | Problem | Empfehlung |

### Hinweise
| # | Kategorie | Datei:Zeile | Problem | Empfehlung |

### Stubs
| # | Command | Zeile | Kritikalität | Empfehlung |

### Positiv
- Was gut umgesetzt ist
```

## Wichtig

- **Keine automatischen Fixes** — nur berichten
- **Immer Datei:Zeile angeben**
- **Priorisieren:** Security > Performance > WCAG > Tauri > Konventionen
- **Stubs explizit auflisten**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haenel881988) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
