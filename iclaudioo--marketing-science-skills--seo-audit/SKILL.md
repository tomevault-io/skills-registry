---
name: seo-audit
description: Wanneer de gebruiker een SEO-audit, -review of -diagnose wil uitvoeren op een website. Ook bij 'SEO audit,' 'SEO-audit,' 'technische SEO,' 'technical SEO,' 'waarom rank ik niet,' 'SEO-problemen,' 'SEO issues,' 'on-page SEO,' 'meta tags review,' 'SEO health check,' 'SEO-gezondheidscheck,' 'Core Web Vitals,' 'crawlability,' 'indexatie.' Voor het bouwen van pagina's op schaal voor keyword targeting, zie seo-marketing. Voor structured data, zie seo-marketing. Voor AI-zoekoptimalisatie, zie ai-seo. Use when this capability is needed.
metadata:
  author: iclaudioo
---

# SEO audit

Je bent een expert in zoekmachineoptimalisatie. Je doel is SEO-problemen identificeren en actionable aanbevelingen geven om organische zoekprestaties te verbeteren.

## Context laden

Als `.agents/marketing-context.md` bestaat, lees dit eerst.
Gebruik die context voor bestaande positionering, doelgroep, sector en websitestructuur.

## Initiële beoordeling

Begrijp deze context voordat je audit (vraag als het niet gegeven is):

1. **Sitecontext**
   - Wat voor type site? (SaaS, e-commerce, blog, etc.)
   - Wat is het primaire bedrijfsdoel voor SEO?
   - Welke keywords/onderwerpen zijn prioriteit?

2. **Huidige staat**
   - Bekende problemen of zorgen?
   - Huidig organisch verkeersniveau?
   - Recente wijzigingen of migraties?

3. **Scope**
   - Volledige site-audit of specifieke pagina's?
   - Technisch + on-page, of één focusgebied?
   - Toegang tot Search Console / analytics?

---

## Auditframework

### Belangrijk: schema markup detectiebeperking

**`web_fetch` en `curl` kunnen structured data / schema markup niet betrouwbaar detecteren.**

Veel CMS-plugins (AIOSEO, Yoast, RankMath) injecteren JSON-LD via client-side JavaScript. Dit verschijnt niet in statische HTML of `web_fetch` output (die `<script>` tags stript tijdens conversie).

**Om schema markup accuraat te checken, gebruik een van deze methoden:**
1. **Browser tool**: render de pagina en voer uit: `document.querySelectorAll('script[type="application/ld+json"]')`
2. **Google Rich Results Test**: https://search.google.com/test/rich-results
3. **Screaming Frog export**: als de klant die levert, gebruik die (SF rendert JavaScript)

**Rapporteer nooit "geen schema gevonden" alleen op basis van `web_fetch` of `curl`.** Dit heeft in productie geleid tot foutieve auditbevindingen.

### Prioriteitsvolgorde
1. **Crawlbaarheid en indexatie** (kan Google het vinden en indexeren?)
2. **Technische fundamenten** (is de site snel en functioneel?)
3. **On-page optimalisatie** (is content geoptimaliseerd?)
4. **Contentkwaliteit** (verdient het om te ranken?)
5. **Autoriteit en links** (heeft het geloofwaardigheid?)

---

## Technische SEO audit

### Crawlbaarheid

**Robots.txt**
- Check op onbedoelde blokkades
- Verifieer dat belangrijke pagina's toegestaan zijn
- Check sitemap-referentie

**XML Sitemap**
- Bestaat en is toegankelijk
- Ingediend bij Search Console
- Bevat alleen canonical, indexeerbare URL's
- Regelmatig bijgewerkt
- Correcte formattering

**Sitearchitectuur**
- Belangrijke pagina's binnen 3 klikken van homepage
- Logische hiërarchie
- Interne linkingstructuur
- Geen wezenpagina's (orphan pages)

**Crawlbudget-problemen** (voor grote sites)
- Geparametriseerde URL's onder controle
- Gefacetteerde navigatie correct afgehandeld
- Infinite scroll met paginering-fallback
- Sessie-ID's niet in URL's

### Indexatie

**Indexstatus**
- site:domein.nl check
- Search Console coverage rapport
- Vergelijk geïndexeerd vs. verwacht

**Indexatieproblemen**
- Noindex tags op belangrijke pagina's
- Canonicals die de verkeerde kant op wijzen
- Redirect chains/loops
- Soft 404's
- Dubbele content zonder canonicals

**Canonicalisatie**
- Alle pagina's hebben canonical tags
- Zelfrefererende canonicals op unieke pagina's
- HTTP > HTTPS canonicals
- www vs. non-www consistentie
- Trailing slash consistentie

### Sitesnelheid en Core Web Vitals

**Core Web Vitals**
- LCP (Largest Contentful Paint): < 2.5s
- INP (Interaction to Next Paint): < 200ms
- CLS (Cumulative Layout Shift): < 0.1

**Snelheidsfactoren**
- Serverresponstijd (TTFB)
- Beeldoptimalisatie
- JavaScript-uitvoering
- CSS-levering
- Caching headers
- CDN-gebruik
- Fontlading

**Tools**
- PageSpeed Insights
- WebPageTest
- Chrome DevTools
- Search Console Core Web Vitals rapport

### Mobielvriendelijkheid

- Responsive design (geen apart m. site)
- Tap target afmetingen
- Viewport geconfigureerd
- Geen horizontale scroll
- Zelfde content als desktop
- Mobile-first indexering gereedheid

### Beveiliging en HTTPS

- HTTPS over de hele site
- Geldig SSL-certificaat
- Geen mixed content
- HTTP > HTTPS redirects
- HSTS header (bonus)

### URL-structuur

- Leesbare, beschrijvende URL's
- Keywords in URL's waar natuurlijk
- Consistente structuur
- Geen onnodige parameters
- Kleine letters en koppeltekengescheiden

---

## On-page SEO audit

### Title tags

Gebruikers scannen SERP-resultaten in System 1 modus (Kahneman, 2011): snel, automatisch, op basis van patroonherkenning. Ze lezen niet, ze scannen. Dit betekent dat title tags ontworpen moeten worden voor snelle herkenning, niet voor informatiedichtheid.

**Check op:**
- Unieke titels voor elke pagina
- Primaire keyword nabij het begin (System 1 scant van links naar rechts)
- 50-60 tekens (zichtbaar in SERP)
- Overtuigend en klikwaardig. Cialdini-principes voor CTR-optimalisatie:
  - **Autoriteit**: "Expert gids," "Volgens [sector-autoriteit]," certificeringen in de titel
  - **Sociale bewijskracht**: "Door 5.000+ bedrijven gebruikt," "Best beoordeeld"
  - **Schaarste/urgentie**: "2026 update," "Nieuw" (alleen als waarheidsgetrouw)
- Merknaam plaatsing (einde, meestal). Bij sterke merken verhoogt merknaam de CTR (autoriteitseffect).

**Veelvoorkomende problemen:**
- Dubbele titels
- Te lang (afgekapt, de System 1 scan wordt onderbroken)
- Te kort (gemiste kans op keyword en Cialdini-trigger)
- Keyword stuffing
- Volledig ontbrekend

### Meta descriptions

Meta descriptions zijn de "verkooptekst" in de SERP. Ze beïnvloeden CTR direct, ook al zijn ze geen directe rankingfactor.

**Check op:**
- Unieke beschrijvingen per pagina
- 150-160 tekens
- Bevat primaire keyword (Google markeert het vetgedrukt, wat pre-attentive aandacht trekt)
- Duidelijke waardepropositie. Gebruik Cialdini:
  - **Sociale bewijskracht**: "Vertrouwd door 500+ Belgische KMO's"
  - **Autoriteit**: "Door gecertificeerde experts," "Officiële gids"
  - **Wederkerigheid**: "Gratis download," "Inclusief template"
- Call to action: specifiek en actiegericht ("Bereken je besparing," "Download de checklist")

**Veelvoorkomende problemen:**
- Dubbele beschrijvingen
- Automatisch gegenereerde rotzooi
- Te lang/kort
- Geen overtuigende reden om te klikken (geen Cialdini-trigger, geen CTA)

### Headingstructuur

**Check op:**
- Eén H1 per pagina
- H1 bevat primaire keyword
- Logische hiërarchie (H1 > H2 > H3)
- Headings beschrijven content
- Niet alleen voor styling

**Veelvoorkomende problemen:**
- Meerdere H1's
- Niveaus overslaan (H1 > H3)
- Headings alleen voor styling gebruikt
- Geen H1 op pagina

### Contentoptimalisatie

**Primaire pagina-content**
- Keyword in eerste 100 woorden
- Gerelateerde keywords natuurlijk gebruikt
- Voldoende diepte/lengte voor onderwerp
- Beantwoordt zoekintentie
- Beter dan concurrenten

**Dunne content problemen**
- Pagina's met weinig unieke content
- Tag/categoriepagina's zonder waarde
- Doorway pages
- Dubbele of bijna-dubbele content

### Beeldoptimalisatie

**Check op:**
- Beschrijvende bestandsnamen
- Alt-tekst op alle afbeeldingen
- Alt-tekst beschrijft de afbeelding
- Gecomprimeerde bestandsgroottes
- Moderne formaten (WebP)
- Lazy loading geïmplementeerd
- Responsive afbeeldingen

### Interne linking

**Check op:**
- Belangrijke pagina's goed gelinkt
- Beschrijvende ankertekst
- Logische linkrelaties
- Geen kapotte interne links
- Redelijk aantal links per pagina

**Veelvoorkomende problemen:**
- Wezenpagina's (geen interne links)
- Overgeoptimaliseerde ankertekst
- Belangrijke pagina's begraven
- Excessieve footer/sidebar links

### Keyword targeting

**Per pagina**
- Duidelijk primair keyword doel
- Titel, H1, URL op lijn
- Content bevredigt zoekintentie
- Concurreert niet met andere pagina's (kannibalisatie)

**Sitebreed**
- Keyword mapping document
- Geen grote gaten in dekking
- Geen keyword-kannibalisatie
- Logische thematische clusters

---

## Contentkwaliteitsbeoordeling

### Schwartz awareness stages als content audit lens

Beoordeel elke pagina niet alleen op SEO-kwaliteit, maar ook op alignment met het bewustzijnsniveau van de zoeker (Schwartz, 1966). Mismatch tussen awareness stage en content is een veelvoorkomende oorzaak van hoge bounce rates bij goede rankings.

| Awareness stage | Zoekgedrag | Content moet | Veelgemaakte fout |
|----------------|-----------|-------------|-------------------|
| **Unaware** | Bredt informatief ("hoe werkt X") | Educeren, probleem benoemen | Direct product pushen |
| **Problem Aware** | Probleem-gericht ("X oplossen") | Probleem valideren, oplossingsrichtingen tonen | Te snel naar één oplossing springen |
| **Solution Aware** | Oplossing-gericht ("beste X tool") | Vergelijken, differentiëren | Geen concurrentieperspectief |
| **Product Aware** | Merk-gericht ("bedrijfsnaam review") | Bezwaren wegnemen, social proof | Alleen features, geen proof points |
| **Most Aware** | Actie-gericht ("bedrijfsnaam prijzen") | Friction verwijderen, CTA | Te veel informatie, geen actiepad |

Gebruik deze lens bij de content audit: rankt een "Problem Aware" pagina maar biedt die "Most Aware" content (directe sales push)? Dan is de bounce rate geen SEO-probleem maar een content-strategie probleem.

### E-E-A-T signalen

**Experience (ervaring)**
- Ervaring uit eerste hand aangetoond
- Originele inzichten/data
- Echte voorbeelden en case studies

**Expertise**
- Auteursreferenties zichtbaar
- Accurate, gedetailleerde informatie
- Correct bronvermelde beweringen

**Authoritativeness (gezag)**
- Erkend in het vakgebied
- Geciteerd door anderen
- Sectorale credentials

**Trustworthiness (betrouwbaarheid)**
- Accurate informatie
- Transparant over bedrijf
- Contactinformatie beschikbaar
- Privacybeleid, voorwaarden
- Beveiligde site (HTTPS)

### Contentdiepte

- Uitgebreide dekking van onderwerp
- Beantwoordt vervolgvragen
- Beter dan top-rankende concurrenten
- Bijgewerkt en actueel

### Gebruikersbetrokkenheidssignalen

- Tijd op pagina
- Bounce rate in context (check awareness stage alignment voor je conclusies trekt)
- Pagina's per sessie
- Terugkeerbezoeken

---

## Ehrenberg-Bass compliance

SEO is in essentie een acquisitiekanaal. Vanuit Ehrenberg-Bass perspectief:

- **Mentale beschikbaarheid**: SEO bouwt mentale beschikbaarheid door je merk te koppelen aan relevante zoekopdrachten (Category Entry Points). Elke keyword waarvoor je rankt is een CEP.
- **Bereik > frequentie**: optimaliseer voor zoveel mogelijk relevante keywords (bereik), niet alleen voor één kernterm (frequentie). Light buyers vinden je via lange-staart zoekopdrachten.
- **Fysieke beschikbaarheid**: ranking op pagina 1 is de digitale equivalent van schapplaatsing. Als je niet zichtbaar bent op het moment van zoeken, besta je niet.
- **Distinctive Brand Assets**: zorg dat je merknaam, visuele identiteit en kernboodschap consistent zijn in title tags, meta descriptions en OG tags. Dit zijn touchpoints waar DBAs herkenning moeten triggeren.
- **Category Entry Points**: gebruik je SEO-audit om te identificeren voor welke CEPs je al rankt en welke je mist. Dit is waardevoller dan puur technische metrics.
- **Double jeopardy**: kleinere merken zullen voor dezelfde keywords lagere CTR's zien dan grotere merken (minder bekendheid = minder klikken). Dit maakt long-tail targeting extra belangrijk voor kleinere spelers.

## Backlink en autoriteitsanalyse

Backlinks zijn een van de sterkste rankingfactoren. Beoordeel op kwaliteit, niet kwantiteit.

**Kernmetrics:** referring domains (diversiteit > volume), Domain Authority/Rating, ankertekstverdeling (natuurlijk profiel = mix van merk, URL, keyword, generiek; >30% exact-match = spam-signaal), link velocity en toxic links.

**Concurrentiebenchmark:** vergelijk referring domains met top 3 concurrenten. Bij 3-5x achterstand is pure on-page optimalisatie onvoldoende.

**Linkgaps:** welke domeinen linken naar concurrenten maar niet naar jou? Dit zijn de meest haalbare targets.

**Tools:** Ahrefs, Semrush, Moz (betaald). Google Search Console toont een deel gratis.

---

## Live site analyse via Firecrawl

Firecrawl MCP maakt het mogelijk om live websites te crawlen en technische SEO-data te extraheren zonder externe tools. Dit is bijzonder waardevol voor snelle audits waar je geen toegang hebt tot Screaming Frog of Search Console.

### Wanneer Firecrawl gebruiken

- Bij technische SEO audits waar je de live site wilt crawlen
- Bij content audits waar je pagina-inhoud wilt extraheren
- Bij competitive SEO analyse (concurrent sites crawlen)
- Bij het verifiëren van schema markup, meta tags, headers op live pagina's

### Firecrawl commando's voor SEO audit

**1. Enkele pagina analyseren**

```
Gebruik Firecrawl om [URL] te lezen en analyseer:
- Title tag en meta description
- H1-H6 heading structuur
- Schema markup aanwezigheid
- Interne en externe links
- Content lengte en kwaliteit
```

**2. Site crawl voor technische audit**

```
Gebruik Firecrawl om [domein] te crawlen (max 50 pagina's) en rapporteer:
- Pagina's zonder meta description
- Duplicate title tags
- Ontbrekende H1 tags
- Broken internal links
- Pagina's zonder schema markup
```

**3. Competitor vergelijking**

```
Gebruik Firecrawl om [concurrent URL] te lezen en vergelijk met [eigen URL]:
- Content diepte en structuur
- Keyword targeting
- Schema markup completeness
- Internal linking patterns
```

### Workflow: Firecrawl-powered SEO audit

1. Start met Firecrawl crawl van de homepage + 20-50 belangrijkste pagina's
2. Extraheer technische data (titles, metas, headings, schema, links)
3. Analyseer volgens de bestaande audit-checklist in deze skill
4. Genereer rapport met bevindingen en prioriteiten
5. Vergelijk optioneel met concurrent via tweede crawl

### Beperkingen

- Firecrawl crawlt de live site, niet de Google index. Indexatie-problemen vereisen Google Search Console.
- JavaScript-rendered content wordt wel opgepikt door Firecrawl.
- Respecteer robots.txt en crawl-limieten bij concurrent-analyse.

---

## Veelvoorkomende problemen per sitetype

**SaaS:** dunne product-/featurepagina's, blog niet gelinkt aan product, ontbrekende vergelijkingspagina's.
**E-commerce:** dunne categoriepagina's, duplicate productcontent, gefacetteerde navigatie duplicaten, ontbrekend productschema.
**Content/blog:** verouderde content, keyword-kannibalisatie, geen topic clusters, zwakke interne linking.
**Lokaal:** inconsistente NAP, ontbrekend lokaal schema, geen locatiepagina's.

---

## Outputformaat

Per bevinding: **Probleem** (wat), **Impact** (hoog/midden/laag), **Bewijs** (data/meting), **Fix** (concrete oplossing), **Prioriteit** (P1-P4).

Rapportstructuur: samenvatting met top 5 issues, technische bevindingen, on-page bevindingen, contentbevindingen, geprioriteerd actieplan.

---

## Referenties

- [AI-schrijfdetectie](references/ai-writing-detection.md): veelvoorkomende AI-schrijfpatronen om te vermijden (em dashes, overgebruikte frasen, opvulwoorden)
- Voor AI-zoekoptimalisatie (AEO, GEO, LLMO, AI Overviews), zie de **ai-seo** skill

---

## Tools waarnaar verwezen wordt

**Gratis tools**
- Google Search Console (essentieel)
- Google PageSpeed Insights
- Bing Webmaster Tools
- Rich Results Test (**gebruik deze voor schemavalidatie: rendert JavaScript**)
- Mobile-Friendly Test
- Schema Validator

> **Opmerking over schemadetectie:** `web_fetch` stript `<script>` tags (inclusief JSON-LD) en kan door JS-geïnjecteerd schema niet detecteren. Gebruik altijd de browser tool, Rich Results Test of Screaming Frog voor schemacontroles. Zie de waarschuwing bovenaan de Auditframework-sectie.

**Betaalde tools** (indien beschikbaar)
- Screaming Frog
- Ahrefs / Semrush
- Sitebulb
- ContentKing

---

## Gerelateerde skills

- **ai-seo**: voor het optimaliseren van content voor AI-zoekmachines (AEO, GEO, LLMO)
- **seo-marketing**: voor keyword-strategie, technische SEO en structured data implementatie
- **digital-marketing**: voor paginahiërarchie, navigatieontwerp en URL-structuur
- **content-marketing**: voor contentplanning en -distributie die SEO ondersteunt
- **conversion-optimization**: voor het optimaliseren van pagina's voor conversie (niet alleen ranking)
- **marketing-analytics**: voor het meten van SEO-prestaties

---

## Scoring rubric

| Dimensie | 1-3 (zwak) | 4-6 (voldoende) | 7-8 (goed) | 9-10 (excellent) |
|----------|-----------|-----------------|------------|-------------------|
| **Auditscope** | Slechts 1-2 pijlers geadresseerd | 3-4 pijlers, maar oppervlakkig | Alle 5 pijlers (crawl, technisch, on-page, content, autoriteit) | Volledig met E-E-A-T, kannibalisatie-check en cross-pijler verbanden |
| **Bewijs en data** | Bevindingen zonder meting of benchmark | Enkele metingen, generieke benchmarks | Elke bevinding met meting, benchmark en bron | Site-specifieke data, trend-analyse, impact-kwantificering |
| **Prioritering** | Platte lijst zonder volgorde | Basis hoog/midden/laag | Impact/effort matrix met duidelijke P1-P3 | Gefaseerd actieplan met verwachte impact, effort-schatting en eigenaar |
| **Actionability** | Generieke aanbevelingen ("verbeter SEO") | Specifieke maar niet implementeerbare tips | Concrete fixes met voorbeeldcode of templates | Implementatieklare fixes met before/after, verwacht resultaat en meetplan |
| **Schema audit methode** | Schema beoordeeld via web_fetch (onbetrouwbaar) | Schemadetectie-beperking benoemd maar niet geaddresseerd | Correcte methode gebruikt (browser, Rich Results Test) | Schema gevalideerd, fouten geidentificeerd, verbeterde markup geleverd |
| **Strategische context** | Puur technische audit zonder businesscontext | Basis businessdoel benoemd | Bevindingen gelinkt aan businessdoelen en organische kansen | Audit als strategisch document: SEO roadmap gelinkt aan revenue impact |
| **Behavioral science** | Geen aandacht voor SERP-gedrag of awareness stages | Basis CTR-optimalisatie benoemd | Cialdini-principes in title/meta, Schwartz stages in content audit | System 1 SERP-design, awareness-aligned content, backlink autoriteitsanalyse |

---

## Zelfcheck voor oplevering

Voordat je de audit als definitief beschouwt:

1. [ ] Alle 5 auditpijlers zijn geadresseerd (crawlbaarheid, technisch, on-page, content, autoriteit)
2. [ ] Schema markup is NIET beoordeeld via alleen `web_fetch` of `curl`
3. [ ] Core Web Vitals drempels zijn gecheckt (LCP < 2.5s, INP < 200ms, CLS < 0.1)
4. [ ] Elk probleem bevat: probleem, impact, bewijs, fix en prioriteit
5. [ ] Geen valse "geen schema gevonden" bevindingen op basis van onbetrouwbare methoden
6. [ ] Geprioriteerd actieplan is opgenomen met kritieke fixes bovenaan
7. [ ] E-E-A-T signalen zijn beoordeeld voor de belangrijkste pagina's
8. [ ] Keyword-kannibalisatie is gecheckt (ranken meerdere pagina's voor hetzelfde keyword?)
9. [ ] Interne linking structuur is geëvalueerd op wezenpagina's en dode links

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iclaudioo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
