---
name: verify-code
description: Schreibt Code, findet Bugs, schreibt neu. Run '/verify-code', 'code pruefen', oder 'verification loop'. Use when this capability is needed.
metadata:
  author: leonardsylvester
---

# Verify Code Skill (Verification Loop)

Implementiert eine selbst-korrigierende Code-Schleife: Schreiben, Bugs finden, verbessern. Basiert auf Anthropic's "Verification Loop" Best Practice.

## Wann ausfuehren

- Bei Code-Aufgaben wo Qualitaet wichtig ist
- Wenn User sagt "verify-code", "code pruefen", oder "/verify-code"
- Bei komplexen Funktionen mit vielen Edge Cases
- Bei sicherheitskritischem Code

## Workflow

### 1. Code schreiben (Erste Version)

Implementiere die angeforderte Funktion/Feature wie gewohnt.

```
## Erste Implementation

[Code hier]
```

### 2. Selbst-Analyse durchfuehren

Analysiere den gerade geschriebenen Code auf **genau 3 potenzielle Problem-Kategorien**:

**a) Edge Cases**
- Was passiert bei leerem Input?
- Was bei null/undefined?
- Was bei sehr grossen Werten?
- Was bei unerwarteten Typen?
- Was bei Concurrent Access?

**b) Sicherheitsluecken**
- Input Validation?
- SQL/Command Injection moeglich?
- XSS Risiken?
- Sensitive Daten exponiert?
- Authentication/Authorization korrekt?

**c) Performance-Probleme**
- Unnoetige Loops oder Rekursionen?
- Memory Leaks moeglich?
- Blocking Operations?
- Fehlende Caching-Moeglichkeiten?
- N+1 Query Probleme?

### 3. Probleme dokumentieren

```
## Gefundene Probleme

### Edge Cases
1. [Problem 1]: [Beschreibung]
2. [Problem 2]: [Beschreibung]

### Sicherheit
1. [Problem 1]: [Beschreibung]

### Performance
1. [Problem 1]: [Beschreibung]
```

Falls keine Probleme gefunden: Explizit bestaetigen warum der Code sicher ist.

### 4. Code neu schreiben

Schreibe den Code neu mit allen Fixes:

```
## Verbesserte Implementation

[Verbesserter Code hier]
```

### 5. Verbesserungen erklaeren

```
## Aenderungen

| Problem | Loesung | Zeile |
|---------|---------|-------|
| [Edge Case 1] | [Fix] | [L42] |
| [Security Issue] | [Fix] | [L15] |
| [Performance] | [Fix] | [L28] |
```

## Beispiel-Ablauf

### User Request
"Schreibe eine Funktion die User-Input validiert"

### Erste Implementation
```typescript
function validateEmail(email: string): boolean {
  return email.includes('@');
}
```

### Gefundene Probleme

**Edge Cases:**
1. Leerer String gibt false, aber keine hilfreiche Fehlermeldung
2. Null/undefined wirft TypeError

**Sicherheit:**
1. Regex fehlt - "test@" wuerde als gueltig erkannt
2. Keine Laengenbegrenzung (DoS durch sehr langen Input moeglich)

**Performance:**
1. Keine (einfache Funktion)

### Verbesserte Implementation
```typescript
function validateEmail(email: string | null | undefined): { valid: boolean; error?: string } {
  if (!email) {
    return { valid: false, error: 'Email ist erforderlich' };
  }

  if (email.length > 254) {
    return { valid: false, error: 'Email ist zu lang' };
  }

  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(email)) {
    return { valid: false, error: 'Ungueltige Email-Adresse' };
  }

  return { valid: true };
}
```

### Aenderungen

| Problem | Loesung | Zeile |
|---------|---------|-------|
| Null/undefined | Null-Check mit Fehlermeldung | L2-4 |
| DoS durch langen Input | Laengenbegrenzung auf 254 Zeichen | L6-8 |
| Schwache Validierung | Proper Email Regex | L10-13 |
| Keine Fehlerdetails | Return-Objekt mit error Property | Alle |

## Wichtige Regeln

- **IMMER** den Loop komplett durchlaufen (nicht abkuerzen)
- **MINDESTENS** 1 Problem pro Kategorie suchen (oder explizit begruenden warum keins existiert)
- **NIEMALS** den ersten Code als "gut genug" akzeptieren
- **KONKRETE** Fixes zeigen, nicht nur Probleme benennen
- Bei trivialen Funktionen: Verkuerzte Analyse ok, aber trotzdem durchfuehren

## Wann NICHT verwenden

- Bei einfachen Einzeilern ohne Logik
- Bei reinen Config-Aenderungen
- Wenn User explizit "schnell" oder "quick fix" sagt

## Typische Nutzung

User: `/verify-code Implementiere eine Rate-Limiting Funktion`

Response:
1. Erste Implementation schreiben
2. Edge Cases, Security, Performance analysieren
3. Probleme auflisten
4. Verbesserten Code schreiben
5. Aenderungen erklaeren

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leonardsylvester) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
