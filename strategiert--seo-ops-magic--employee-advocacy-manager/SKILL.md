---
name: employee-advocacy-manager
description: Creates and manages employee advocacy content for personal LinkedIn profiles. Provides ready-to-post content with personalization guidelines. Handles tone adaptation for individual voices while maintaining brand alignment. Use when creating content for employee amplification programs.
metadata:
  author: strategiert
---

# Employee Advocacy Manager

Erstellt und verwaltet Employee-Advocacy-Content für persönliche Profile.

## Quick Start

```
Input: Company Content + Employee Personas + Guidelines
Output: Personalisierbare Posts + Posting-Guide + Tracking
```

## Was ist Employee Advocacy?

```
Mitarbeiter teilen Unternehmensinhalte über ihre
persönlichen Social-Media-Profile.

Vorteile:
- 8x mehr Engagement als Company Posts
- 561% mehr Reichweite
- Authentischer und vertrauenswürdiger
- Erweitert das Netzwerk exponentiell
```

## Advocacy-Content-Typen

| Typ | Beschreibung | Personalisierung |
|-----|--------------|------------------|
| Company News | Produkt-Launches, Updates | Eigene Perspektive hinzufügen |
| Thought Leadership | Industry Insights | Eigene Meinung ergänzen |
| Culture | Team, Events, Werte | Persönliche Erfahrung |
| Job Openings | Offene Stellen | Warum hier arbeiten? |
| Content Amplification | Blogs, Webinare | Was gelernt? |

## Output Format

```json
{
  "advocacy_content": {
    "id": "ADV-2024-001",
    "source": "Blog: Content Marketing Guide",
    "platforms": ["linkedin", "twitter"],
    "release_date": "2024-01-15"
  },
  "base_post": {
    "hook": "Content Marketing verändert sich. Hier sind die wichtigsten Trends für 2024.",
    "body": "In den letzten Monaten haben wir beobachtet, dass...",
    "cta": "Link zum vollständigen Guide 👇",
    "link": "https://example.com/guide"
  },
  "personalization_options": {
    "hooks": [
      "Als Content Marketer beobachte ich...",
      "Diese 3 Trends werden 2024 alles verändern:",
      "Mein Team hat gerade diesen Guide veröffentlicht:"
    ],
    "personal_angles": [
      "Teile deine eigene Erfahrung mit Content Marketing",
      "Welcher Trend überrascht dich am meisten?",
      "Ergänze mit einem Beispiel aus deiner Arbeit"
    ],
    "ctas": [
      "Was sind eure Content-Pläne für 2024?",
      "Link zum Guide in den Kommentaren",
      "Welchen Trend seht ihr noch?"
    ]
  },
  "employee_versions": [
    {
      "role": "Marketing Manager",
      "suggested_post": "Als Marketing Manager bei [Company] sehe ich täglich, wie sich Content Marketing verändert...",
      "personalization_tips": "Ergänze mit einem konkreten Beispiel aus deiner täglichen Arbeit"
    },
    {
      "role": "Sales Rep",
      "suggested_post": "Unsere Kunden fragen immer öfter nach Content-Strategien. Mein Team hat jetzt den perfekten Guide...",
      "personalization_tips": "Erwähne ein Kundengespräch (anonymisiert)"
    }
  ],
  "guidelines": {
    "do": [
      "Eigene Perspektive hinzufügen",
      "Authentisch bleiben",
      "Mit Community interagieren"
    ],
    "dont": [
      "Copy-Paste ohne Anpassung",
      "Zu werblich",
      "Gegen Unternehmensrichtlinien"
    ]
  }
}
```

## Personalisierungs-Levels

### Level 1: Light Touch (schnell)

```
Mitarbeiter:
- Fügt eigenen Einleitungssatz hinzu
- Rest bleibt wie vorgeschlagen
- Minimal Effort

Beispiel:
"Super stolz auf mein Team! 👇

[Original Company Post]"
```

### Level 2: Moderate (empfohlen)

```
Mitarbeiter:
- Eigener Hook
- Persönliche Perspektive
- Angepasster CTA

Beispiel:
"Nach 5 Jahren im Content Marketing sehe ich einen klaren Trend:
Qualität > Quantität.

Mein Team hat die wichtigsten Insights in diesem Guide zusammengefasst.

Was ich besonders spannend finde: [persönlicher Insight]

Link in den Kommentaren 👇"
```

### Level 3: Fully Personalized (best)

```
Mitarbeiter:
- Komplett eigener Text
- Referenziert Unternehmensinhalt
- Eigene Story/Erfahrung

Beispiel:
"Letzte Woche hatte ich ein Gespräch mit einem Kunden, das
mich zum Nachdenken gebracht hat.

Er fragte: 'Funktioniert Content Marketing noch?'

Meine Antwort: Ja, aber anders als 2020.

Hier sind die 3 größten Veränderungen, die ich beobachte:
[eigene Insights]

Unser neuer Guide geht noch tiefer: [Link]

Was sind eure Beobachtungen?"
```

## Rollen-spezifische Templates

### Für Leadership/Executives

```
Fokus: Vision, Strategie, Culture

Template:
"Als [Rolle] bei [Company] denke ich oft über [Thema] nach.

[Persönliche Reflexion oder Insight]

Stolz auf mein Team, das [Achievement].

[CTA/Link]"
```

### Für Marketing/Sales

```
Fokus: Insights, Learnings, Trends

Template:
"Das hat sich bei unseren Kunden verändert:

→ [Beobachtung 1]
→ [Beobachtung 2]
→ [Beobachtung 3]

Wir haben das in [Content] zusammengefasst.

[Link]"
```

### Für Tech/Produkt

```
Fokus: Innovation, Features, How-To

Template:
"Wir haben gerade [Feature] gelauncht.

Warum ich so excited bin:
[Technischer/Product Insight]

Hier ist, wie es funktioniert:
[Link]"
```

### Für HR/People

```
Fokus: Culture, Hiring, Team

Template:
"Warum ich bei [Company] arbeite:

[Persönliche Story]

Wir suchen [Rollen]. DM mich, wenn du Fragen hast!

[Link zu Jobs]"
```

## Content-Kalender für Advocacy

### Wöchentlicher Rhythmus

```
MONTAG:
└── Thought Leadership / Industry Insight

MITTWOCH:
└── Company News / Product Update

FREITAG:
└── Culture / Team / Behind-Scenes
```

### Content-Rotation

```
Woche 1: Blog-Content verstärken
Woche 2: Company Milestone/News
Woche 3: Thought Leadership
Woche 4: Culture/Hiring
```

## Gamification & Motivation

### Leaderboard-Metriken

```
Tracking:
- Anzahl Posts
- Engagement generiert
- Reichweite
- Klicks auf Links

Levels:
🥉 Bronze: 4 Posts/Monat
🥈 Silber: 8 Posts/Monat
🥇 Gold: 12 Posts/Monat
💎 Diamond: Top 10% Engagement
```

### Incentives

```
Mögliche Rewards:
- Shoutout in Company Meeting
- Zusätzlicher Urlaubstag
- Kleine Prämie
- LinkedIn Premium
- Training/Konferenz
```

## Compliance & Guidelines

### Must-Haves

```
✅ Persönlich bleiben (nicht wie Company sprechen)
✅ Fakten korrekt
✅ Disclosure bei gesponserten Inhalten
✅ Vertrauliche Infos schützen
✅ Professionell bleiben
```

### Verboten

```
❌ Nicht-öffentliche Infos teilen
❌ Konkurrenten schlecht reden
❌ Kundendaten erwähnen
❌ Politische/kontroverse Themen
❌ Im Namen des Unternehmens sprechen
```

### Disclosure

```
Bei Produkten/Services des Arbeitgebers:

"Disclaimer: Ich arbeite bei [Company]"

oder

"Volle Transparenz: Ich bin bei [Company] angestellt"
```

## Erfolgs-Metriken

### Individual Level

```
- Posts pro Monat
- Durchschnittliches Engagement
- Follower-Wachstum
- Link Clicks
```

### Program Level

```
- Teilnahmequote (% der Mitarbeiter)
- Gesamt-Reichweite
- Traffic zu Website
- Leads generiert
- Brand Mentions
```

### Reporting Template

```markdown
# Employee Advocacy Report - [Monat]

## Programm-Overview
- Aktive Advocates: X / Y Mitarbeiter (X%)
- Total Posts: X
- Gesamt-Reichweite: X

## Top Performers
1. [Name] - X Posts, X Engagement
2. [Name] - X Posts, X Engagement
3. [Name] - X Posts, X Engagement

## Content Performance
- Best Topic: [Topic]
- Best Post: [Link]
- Avg. Engagement Rate: X%

## Impact
- Website Traffic from Advocacy: X Visits
- Leads attributed: X
- Job Applications from Shares: X

## Learnings
- [Learning 1]
- [Learning 2]
```

## Checkliste

### Für Program Manager
- [ ] Wöchentlichen Content bereitstellen?
- [ ] Personalisierungs-Optionen klar?
- [ ] Guidelines aktuell?
- [ ] Tracking eingerichtet?
- [ ] Advocates informiert?

### Für Employees
- [ ] Post personalisiert?
- [ ] Eigene Perspektive hinzugefügt?
- [ ] Authentisch formuliert?
- [ ] Compliance beachtet?
- [ ] Mit Kommentaren interagiert?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strategiert) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
