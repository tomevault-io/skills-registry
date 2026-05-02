---
name: blueprint-document-reference
description: Riferimento per la creazione e la modifica di documenti di Adobe Digital Experience Blueprint. Da utilizzare per creare nuovi blueprint, aggiungere pagine blueprint o quando l’utente chiede informazioni su struttura, sezioni, modelli blueprint o riferimenti ad Adobe Experience League. Use when this capability is needed.
metadata:
  author: adobedocs
---


# Riferimento documento blueprint

Utilizza questa abilità durante la creazione o la modifica di documenti blueprint in questo archivio. I blueprint sono implementazioni ripetibili che affrontano problemi di business consolidati e includono diagrammi di architettura, considerazioni tecniche e collegamenti alla relativa documentazione di Adobe Experience League.

## Quando applicare

- Creazione di un nuovo documento blueprint o di una pagina di panoramica blueprint
- Aggiunta o ristrutturazione di sezioni in una blueprint esistente
- Collegamento o citazione della documentazione di Adobe Experience League
- Allineamento di nuovi contenuti alle convenzioni blueprint (contenuti introduttivi, intestazioni, diagrammi)

## Riferimento rapido

1. **Scopo del documento**: i blueprint forniscono l&#39;architettura del sistema e del flusso di dati per mostrare come Adobe Experience Platform e le applicazioni sono integrate. Sono visive e tecniche, non di marketing.
2. **Sezioni**: utilizzare le sezioni standard del modello; omettere solo quando non applicabile (vedere [reference.md](reference.md)).
3. **Experience League**: preferisci il collegamento ad Experience League per documenti di prodotto, API, guardrail e tutorial. Utilizza URL completi; consulta [reference.md](reference.md) per i pattern e la formattazione degli URL.
4. **Struttura dell&#39;archivio**: i blueprint sono attivi in `help/blueprints/`. Aggiorna `help/blueprints/TOC.md` quando aggiungi o sposti pagine blueprint.

## Modello documento

Ogni pagina blueprint deve seguire questa struttura. Includi solo le sezioni applicabili.

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

Per le pagine di panoramica o hub, utilizza una struttura più breve: introduzione, casi d’uso (o schede), immagine dell’architettura, tabella di scenari/pattern, prerequisiti, guardrail, documentazione correlata. Per alcuni esempi, vedere le panoramiche esistenti in `help/blueprints/`.

## Frontmatter

| Campo | Obbligatorio | Note |
|-------|----------|--------|
| `title` | Sì | Breve; utilizza `[!DNL Product Name]` per i nomi di prodotto in base allo stile Adobe |
| `description` | Sì | Una frase; usato nelle ricerche e nelle carte |
| `solution` | Sì | Prodotto principale (ad es. Real-Time Customer Data Platform, Journey Optimizer) |
| `exl-id` | Sì | UUID; lascia vuoto per le nuove pagine |
| `doc-type` | Per panoramiche | Usa `overview-page` per le pagine principali della panoramica blueprint |
| `kt` | Facoltativo | ID dell’articolo della knowledge base, se collegato |

## Riferimento ad Adobe Experience League

- **Quando collegare**: collega ad Experience League per la documentazione del prodotto, i riferimenti API, i guardrail, i tutorial e i passaggi di configurazione. Non duplicare lunghe procedure; riepiloga e collega.
- **Formato URL**: utilizzare URL completi. Preferisci `https://experienceleague.adobe.com/docs/?lang=it...` o `https://experienceleague.adobe.com/it/docs/...`. Per i documenti per sviluppatori, `https://developer.adobe.com/...` è valido.
- **Testo collegamento**: usa testo descrittivo (ad esempio &quot;[Crea schemi] (url)&quot; non &quot;Fai clic qui&quot;). Per i nomi dei prodotti nel testo del collegamento, utilizzare `[!DNL Product Name]` quando appropriato.
- **Sezione documentazione correlata**: fine dei blueprint con una sezione &quot;Documentazione correlata&quot; che raggruppa i collegamenti per categoria (ad esempio, configurazioni di destinazione, documentazione di SDK, profilo e segmentazione, tutorial).

Per i pattern URL dettagliati, il raggruppamento di collegamenti e gli esempi, vedi [reference.md](reference.md).

## Elenco di controllo prima dell’invio

- [ ] elemento frontale ha `title`, `description`, `solution`, `exl-id`
- [ ] H1 corrisponde o espande in modo appropriato il titolo
- [ ] Diagramma dell&#39;architettura presente e testo alternativo descrittivo
- [ ] collegamento ai passaggi dell&#39;implementazione ad Experience League in cui si svolgono le procedure
- [ La sezione dei guardrail ] contiene collegamenti ai documenti ufficiali del guardrail di Experience League
- [ ] La sezione della documentazione correlata include collegamenti Experience League rilevanti
- [ ] Le pagine nuove o spostate si riflettono in `help/blueprints/TOC.md`

## Risorse aggiuntive

- Note complete sul modello e sulla sezione: [reference.md](reference.md)
- Blueprint esistenti: `help/blueprints/` (ad esempio `audience-activation/real-time-lookup.md`, `customer-journeys/journey-optimizer/journey-optimizer-overview.md`)
- Sommario e navigazione: `help/blueprints/TOC.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adobedocs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
