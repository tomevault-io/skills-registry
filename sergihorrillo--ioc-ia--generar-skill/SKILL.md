---
name: generar-skill
description: Assistent per a la creació de noves "Skills" per a Antigravity, seguint les millors pràctiques de l'IOC. Use when this capability is needed.
metadata:
  author: sergihorrillo
---

# Skill: Generar Skill

Aquesta skill automatitza la creació de l'estructura base per a noves funcionalitats de l'agent, assegurant que tots els fitxers i carpetes segueixin l'estàndard establert.

## Estructura de Carpetes Estàndard

Cada skill ha de tenir la següent estructura dins de `.agent/skills/<nom_skill>/`:

1.  **`SKILL.md`**: Fitxer principal amb la descripció, metadades (YAML frontmatter) i instruccions d'ús.
2.  **`scripts/`**: Carpeta per a scripts de Python, Lua, PowerShell o Bash que executen la lògica.
3.  **`resources/`**: Carpeta per a plantilles, imatges, fitxers de configuració o bases de dades estàtiques.

## Bones Pràctiques (IOC)

*   **Frontmatter**: Tots els `SKILL.md` han de començar amb un bloc YAML que inclogui `name` i `description`.
*   **Autonomia**: La skill ha de ser el més autònoma possible. Totes les dependències de fitxers han d'estar dins de la seva pròpia carpeta.
*   **Documentació**: Explica clarament com s'activa la skill (frases clau) i quina és la seva utilitat pedagògica per a l'IOC.
*   **Scripts**: Utilitza scripts especialitzats (Python/Lua) per a tasques complexes en lloc de comandes de terminal llargues.

## Procés de Creació Automàtica

Quan se't demani crear una nova skill, segueix aquests passos:

1.  **Directoris**: Crea la carpeta base i les subcarpetes `scripts/` i `resources/`.
2.  **SKILL.md**: Genera un fitxer `SKILL.md` amb el següent esquema:
    ```markdown
    ---
    name: [Nom de la Skill]
    description: [Descripció breu]
    ---
    # Skill: [Nom de la Skill]
    ## Propòsit
    [Explicació detallada]
    ## Com utilitzar-la
    [Instruccions per a l'agent i frases d'activació]
    ```
3.  **Registre**: Afegeix el resum de la nova skill al fitxer `docs/02_AUTOMATITZACIO.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sergihorrillo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
