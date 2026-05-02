---
name: blueprint-document-reference
description: Referens för att skapa och redigera Adobe Digital Experience Design-dokument. Använd när du skapar nya utkast, lägger till designer eller när användaren frågar om designstrukturen, avsnitten, mallarna eller refererar till Adobe Experience League. Use when this capability is needed.
metadata:
  author: adobedocs
---


# Dokumentreferens för utkast

Använd den här kompetensen när du skapar eller redigerar ritningsdokument i den här databasen. Utkast är repeterbara implementeringar som åtgärdar etablerade affärsproblem och innehåller arkitekturdiagram, tekniska överväganden och länkar till relaterad dokumentation för Adobe Experience League.

## När ska jag använda

- Skapa ett nytt dokument eller en översiktssida för utkast
- Lägga till eller omstrukturera avsnitt i en befintlig plan
- Länka till eller citera Adobe Experience League-dokumentation
- Justera nytt innehåll med hjälp av designkonventioner (överordnad text, rubriker, diagram)

## Snabbreferens

1. **Dokumentsyfte**: Med skisser får du system- och dataflödesarkitektur som visar hur Adobe Experience Platform och program är integrerade. De är visuella och tekniska, inte marknadsföring.
2. **Avsnitt**: Använd standardavsnitten från mallen, utelämna bara om de inte är tillämpliga (se [reference.md](reference.md)).
3. **Experience League**: Föredrar att länka till Experience League för produktdokument, API:er, skyddsutkast och självstudiekurser. Använd fullständiga URL:er; se [reference.md](reference.md) för URL-mönster och formatering.
4. **Repo-struktur**: utkast finns under `help/blueprints/`. Uppdatera `help/blueprints/TOC.md` när du lägger till eller flyttar ritningssidor.

## Dokumentmall

Alla ritningssidor ska följa denna struktur. Inkludera endast avsnitt som gäller.

```markdown
---
title: [Short descriptive title]
description: "[One sentence: what this blueprint shows and why it matters.]"
solution: [Product name, e.g. Real-Time Customer Data Platform, Journey Optimizer]
exl-id: [UUID - leave blank if new, this will be auto-generated as part of the Experience League publishing flow]
---
# [H1 - same as title or expanded]

[1–3 paragraphs: what the blueprint covers, key capabilities, and who it’s for.]

## Applications

* [Product 1]
* [Product 2]

## Use cases

* [Use case 1]
* [Use case 2]

## Prerequisites

[Bullets or short paragraphs: required products, config, or setup.]

## Architecture Diagram

<img src="[path to SVG/image]" alt="[Descriptive alt]" style="width:90%; border:1px solid #4a4a4a" class="modal-image" />

## Guardrails

[Link to Experience League guardrails and any blueprint-specific limits.]

## Implementation patterns

[Optional: named patterns with bullets.]

## Implementation steps

1. [Step with link to Experience League where relevant]
2. ...

## Implementation considerations

[Optional: identity, performance, security, etc.]

## Related documentation

[Grouped links to Experience League: product docs, APIs, tutorials.]
```

Använd en kortare struktur för översikts- eller navsidor: inledande, användningsfall (eller tabbar), arkitekturbild, scenario-/mönstertabell, förutsättningar, skyddsritningar, relaterad dokumentation. Se befintliga översikter i `help/blueprints/` för exempel.

## Frontmatter

| Fält | Obligatoriskt | Anteckningar |
|-------|----------|--------|
| `title` | Ja | Kort; använd `[!DNL Product Name]` för produktnamn enligt Adobe-format |
| `description` | Ja | En mening, används i sökningar och kort |
| `solution` | Ja | Primär produkt (t.ex. Real-Time Customer Data Platform, Journey Optimizer) |
| `exl-id` | Ja | UUID; lämna tomt för nya sidor |
| `doc-type` | För översikter | Använd `overview-page` för huvudöversiktssidor för utkast |
| `kt` | Valfritt | Kunskapsbasartikel-ID om den är länkad |

## Referera till Adobe Experience League

- **När ska du länka**: Länka till Experience League för produktdokumentation, API-referenser, utkast, självstudiekurser och konfigurationssteg. Duplicera inte långa procedurer, sammanfatta och länka inte.
- **URL-format**: Använd fullständiga URL:er. Föredra `https://experienceleague.adobe.com/docs/?lang=sv-SE...` eller `https://experienceleague.adobe.com/sv/docs/...`. `https://developer.adobe.com/...` är även giltigt för utvecklardokument.
- **Länka text**: Använd beskrivande text (t.ex. &quot;[Skapa scheman] (url)&quot;, inte &quot;Klicka här&quot;). Använd `[!DNL Product Name]` om det är lämpligt för produktnamn i länktext.
- **Relaterat dokumentationsavsnitt**: Avsluta utkast med ett relaterat dokumentationsavsnitt som grupperar länkar efter kategori (t.ex. målkonfigurationer, SDK-dokumentation, profil och segmentering, självstudiekurser).

Detaljerade URL-mönster, länkgruppering och exempel finns i [reference.md](reference.md).

## Checklista innan överföring

- [ ] Frontmatter har `title`, `description`, `solution`, `exl-id`
- [ ] H1 matchar eller utökar titeln korrekt
- [ ]-arkitekturdiagram finns och alt-textbeskrivning
- [ ] Implementeringssteg länkar till Experience League där procedurer finns
- [ ] Avsnittslänkar för skyddsräcken till officiella Experience League-skyddsdokument
- [ Avsnittet ] Relaterad dokumentation innehåller relevanta Experience League-länkar
- [ ] Nya eller flyttade sidor visas i `help/blueprints/TOC.md`

## Ytterligare resurser

- Fullständiga mall- och avsnittsanteckningar: [reference.md](reference.md)
- Befintliga ritningar: `help/blueprints/` (t.ex. `audience-activation/real-time-lookup.md`, `customer-journeys/journey-optimizer/journey-optimizer-overview.md`)
- Innehåll och navigering: `help/blueprints/TOC.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adobedocs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
