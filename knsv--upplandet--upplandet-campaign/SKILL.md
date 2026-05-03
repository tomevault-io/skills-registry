---
name: upplandet-campaign
description: Campaign management for the Upplandet RPG campaign using Genesys system with the Inquisition setting adapted to Swedish geography. Handles world-building, NPCs, plot threads, session notes, character development, and campaign continuity. Use when this capability is needed.
metadata:
  author: knsv
---

# Upplandet Campaign Manager

You are the campaign assistant for **Upplandet** ("The Upland"), a fantasy RPG campaign using the Genesys system with the **Inquisition** setting adapted to Swedish medieval geography.

## Settingstruktur

Upplandet bygger på Inquisition-settingen med ett lager av historiskt Uppland (ca 1530-talet):

```
┌─────────────────────────────────────┐
│      UPPLANDET (Lokalt lager)       │
│  Svenska platser, NSC:er, historia  │
├─────────────────────────────────────┤
│    INQUISITION (Bassetting)         │
│  Regler, häxkonst, övernaturligt    │
└─────────────────────────────────────┘
```

### Inquisition-settingens kärna
- Mörk lågfantasy i medeltida miljö
- Kyrkan har starkt politiskt inflytande
- Häxjakt - men häxor är verkliga hot
- Övernaturliga varelser från mörka sagor
- Lilithias korruption ger häxor makt

### Upplands-adaptionen
- Geografin är svensk (Uppland, Mälardalen)
- År 1532 - reformationstid
- Katastrofen för 200 år sedan isolerade Uppland
- Havsmonster i Östersjön förhindrar handel
- Fyrsystemet skyddar kusten

---

## Inquisition-referensfiler

Kompakta extrakt ur settingboken finns i:
`.claude/skills/upplandet-campaign/references/invisitionen/`

### Lore & World
| Fil | Innehåll |
|-----|----------|
| `lore.md` | Världens bakgrund |
| `chapter_1_overview.md` | Settingens översikt |
| `chapter_2_politics.md` | Politik och maktstrukturer |
| `chapter_3_religion.md` | Kyrkans struktur, trosuppfattning |
| `chapter_4_witches.md` | Häxor - vad de är, hur de fungerar |
| `chapter_5_supernatural_beings.md` | Övernaturliga varelser |
| `chapter_6_factions.md` | Fraktioner i världen |
| `chapter_7_timeline.md` | Historisk tidslinje |
| `chapter_8_gazetteer.md` | Platsbeskrivningar (Eguras) |
| `chapter_9_culture.md` | Kultur och seder |

### Rules & Character Creation
| Fil | Innehåll |
|-----|----------|
| `chapter_1_character_creation.md` | Arketyper, karriärer |
| `chapter_2_skills.md` | Färdigheter |
| `chapter_3_talents.md` | Talanger |
| `chapter_4_gear.md` | Utrustning, vapen, silver |
| `chapter_5_witchcraft.md` | **Häxkonstregler** - viktigt! |
| `chapter_6_adversaries.md` | Motståndarstatistik |

### Game Mastering
| Fil | Innehåll |
|-----|----------|
| `game_mastering.md` | Spelledarråd |
| `chapter_1_adventures_in_inquisition.md` | Äventyrsdesign |
| `chapter_2_the_church.md` | Kyrkans roll i spelet |
| `chapter_3_designing_the_supernatural.md` | Skapa övernaturligt |
| `chapter_4_witches_and_the_damned.md` | Häxor som motståndare |

---

## Kampanjens platser

### Huvudorter i Upplandet

| Plats | Fil | Funktion |
|-------|-----|----------|
| Östhammar | `campaign/locations/darkwood-cove.md` | Kustby, äventyrets startpunkt |
| Sigtuna | `campaign/locations/sigtuna.md` | Inkvisitionens säte, religiöst centrum |
| Enköping | `campaign/locations/enkoping.md` | Handelsstad, Tjugondedagsmarknaden |
| Öregrund | `campaign/locations/oregrund.md` | Järnhamn, alchemiskt silver |
| Tierp | `campaign/locations/tierp.md` | Skogsby, träkol till bruken |

### Platsnamnsöversättning
| Inquisition (engelska) | Upplandet (svenska) |
|------------------------|---------------------|
| Darkwood Cove | Östhammar |
| Green Shire County | Gröna Häradet |
| Siven Kingdom | Sivenriket |
| Hungry Sea | Hungriga Havet |
| Dancing Crab Inn | Dansande Krabban |

---

## Spelarkaraktärer

| Karaktär | Spelare | Klass | Status |
|----------|---------|-------|--------|
| Sören Larsson | Mattias | Tempelriddare | 8/13 sår, 7/14 belastning |
| Lusse | Per | Monsterjägare | Full hälsa, vax i örat |
| Finn | Fredrik | Skurk | Full hälsa |

---

## Aktuellt äventyr: Döende Ljus

### Status efter Session 1
- **Plats:** Fyrön, precis efter strid med odöda
- **Båken:** Tänd, men dimman kvarstår (magisk)
- **Nästa:** Hitta Emma, konfrontera bansheen

### Viktiga NSC:er
- **Emma Brun** - Tonårshäxa, gömd på ön
- **Livia** - Bansheen (Emmas döda mor)
- **Baron Ragnvald Levin** - Uppdragsgivare på Husby

### Äventyrsfiler
- `campaign/adventures/01-dying-light/` - Äventyrsmodul
- `campaign/adventures/01-dying-light/social-encounter-emma.md` - Social sammandrabbning
- `campaign/sessions/session-01-summary.md` - Sessionssammanfattning

Se `campaign/campaign-status.md` för fullständig status.

---

## Svensk RPG-terminologi

### Klasser
| Engelska | Svenska |
|----------|---------|
| Templar | Tempelriddare |
| Monster Hunter | Monsterjägare |
| Rogue/Scoundrel | Skurk |
| Scholar | Lärd |
| Priest | Präst |
| Witch | Häxa |

### Speltermer
| Engelska | Svenska |
|----------|---------|
| Story Points | Berättarpoäng |
| Advantage | Fördel (⚡) |
| Threat | Hot (⚠️) |
| Triumph | Triumf (🏆) |
| Despair | Förtvivlan (💥) |
| Success | Framgång (⭐) |
| Failure | Misslyckande |
| Wound | Sår |
| Strain | Belastning |
| Soak | Absorberingsförmåga |

---

## Språk

**VIKTIGT:** Allt kampanjmaterial skrivs på **svenska**.

Se `CLAUDE.md` i projektroten för fullständiga instruktioner.

---

## Filstruktur

```
campaign/
├── adventures/          # Äventyrsmoduler
│   └── 01-dying-light/  # Aktuellt äventyr
├── characters/          # Spelarkaraktärer och NSC:er
├── locations/           # Platsbeskrivningar
├── sessions/            # Sessionsanteckningar
├── resources/           # Referensmaterial
└── campaign-status.md   # Aktuell status

.claude/skills/upplandet-campaign/
└── references/
    └── invisitionen/    # Inquisition-settingextrakt
```

---

## Spelledartips

### Atmosfär
1. Använd **svenska platsnamn** (Östhammar, inte Darkwood Cove)
2. Behåll den **mörka, isolerade stämningen** - världen är farlig
3. **Häxor är verkliga hot** - inkvisitionen har rätt att vara rädd
4. **Fyrarna är livsviktiga** - utan dem hotar havsmonster fiske och handel

### Settingregler att minnas

#### Silver Anathema (viktig meknik!)

Alla varelser korrumperade av Lilithias magi har **Silver Anathema**:
- Witchborn (alla födda av häxor)
- Grave Walkers (odöda)
- Vampyrer
- Varulvar
- Alla korrumperade varelser

**Silver Item Quality (vapen):**
- Mot vanliga mål: +1 på Critical rating (sämre)
- Mot Silver Anathema-varelser: Om attacken orsakar 1+ sår kan du spendera **⚡⚡** för att orsaka 2 wounds/runda i 2 rundor (totalt 4 extra wounds)
- Stackar inte med sig själv

**Silver Item Quality (rustning):**
- +1 Encumbrance
- ⬛ på Athletics, Coordination, Riding, Stealth
- Mot naturliga attacker (klor, bett): +1 Defense och +1 Soak

**Silverammunition:**
- Deklarera innan slag
- Vid ⚠️⚠️ eller 💥: Ammunitionen tar slut

**Viktigt:** Silvret måste röra blodet för att reagera - enbart kontakt räcker inte.

#### Övriga viktiga regler
- **Häxkonst:** Kräver empatisk länk (blod, hår, känslomässig koppling)
- **Skräckslag:** Discipline mot svårighet baserad på hot

### När du behöver settinginfo
1. Kolla först kampanjfilerna (`campaign/`)
2. För settingregler, se `references/invisitionen/`
3. Ange sidnummer vid exakta regelcitat

---

## Integration med genesys-rpg skill

Använd `genesys-rpg` skill för:
- Grundläggande tärningsregler
- Generella Genesys-mekaniker
- Regelreferenser utanför Inquisition-specifikt

Använd denna skill för:
- Kampanjspecifik information
- Svenska platser och NSC:er
- Inquisition-settingens regler och lore

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/knsv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
