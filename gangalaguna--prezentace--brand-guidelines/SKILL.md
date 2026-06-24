---
name: brand-guidelines
description: Aplikuje barvy a typografii pro účely školení Use when this capability is needed.
metadata:
  author: gangalaguna
---

# Školení definice

## Přehled

Pro přístup k oficiálním nastavením brandu, barev a stylů stránky použijt tuto dovednost.

**Klíčová slova**: branding, firemní identita, vizuální identita, post-processing, styling, firemní barvy, typografie, značka Anthropic, vizuální formátování, vizuální design

## Pravidla značky

### Barvy

**Hlavní barvy:**

- Tmavá: `#141413` - Primární text a tmavá pozadí
- Světlá: `#faf9f5` - Světlá pozadí a text na tmavém
- Střední šedá: `#b0aea5` - Sekundární prvky
- Světle šedá: `#e8e6dc` - Jemná pozadí

**Doplňkové barvy:**

- Oranžová: `#d97757` - Primární zvýraznění
- Modrá: `#6a9bcc` - Sekundární zvýraznění
- Zelená: `#788c5d` - Terciární zvýraznění

### Typografie

- **Nadpisy**: Poppins (s náhradou Arial)
- **Základní text**: Lora (s náhradou Georgia)
- **Poznámka**: Pro nejlepší výsledky by měly být fonty předinstalované ve vašem prostředí

## Funkce

### Chytré použití fontů

- Aplikuje font Poppins na nadpisy (24pt a větší)
- Aplikuje font Lora na základní text
- Automaticky přepíná na Arial/Georgia, pokud vlastní fonty nejsou dostupné
- Zachovává čitelnost napříč všemi systémy

### Stylování textu

- Nadpisy (24pt+): font Poppins
- Základní text: font Lora
- Chytrý výběr barvy na základě pozadí
- Zachovává hierarchii a formátování textu

### Tvary a doplňkové barvy

- Netextové tvary používají doplňkové barvy
- Cykluje mezi oranžovou, modrou a zelenou
- Udržuje vizuální zájem při zachování značky

## Technické detaily

### Správa fontů

- Používá systémově nainstalované fonty Poppins a Lora, pokud jsou dostupné
- Poskytuje automatickou náhradu Arial (nadpisy) a Georgia (text)
- Není vyžadována instalace fontů - funguje s existujícími systémovými fonty
- Pro nejlepší výsledky předinstalujte fonty Poppins a Lora ve svém prostředí

### Aplikace barev

- Používá RGB hodnoty barev pro přesné sladění značky
- Aplikováno prostřednictvím třídy RGBColor z python-pptx
- Udržuje věrnost barev napříč různými systémy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gangalaguna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
