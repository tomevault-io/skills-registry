---
name: exportar-odt-amb-pandoc
description: Skill per convertir fitxers Markdown a ODT utilitzant Pandoc, incloent imatges amb peus de foto i eliminant scripts de MathJax. Use when this capability is needed.
metadata:
  author: sergihorrillo
---

# Skill: Exportar ODT amb Pandoc

Aquesta skill permet convertir documents Markdown de la IOC a format editable (ODT) mantenint la integritat de les imatges i eliminant elements que només són útils per al PDF.

## Requisits Previs
*   **Pandoc:** Ha d'estar instal·lat al sistema.
*   **Filtre Lua:** S'utilitzarà el fitxer `scripts/filter-images.lua` inclòs en aquesta skill.

## Procés de Conversió

1.  **Preparar el Filtre i Referència:** L'agent utilitzarà el fitxer `filter-images.lua` i el document de referència `resources/reference-arial.odt`.
2.  **Llançar la Comanda:** Executar Pandoc amb el filtre de Lua i el doc de referència.
    ```powershell
    & "C:\Program Files\Pandoc\pandoc.exe" "[Fitxer_Entrada].md" --lua-filter="[Ruta_al_Filtre]/filter-images.lua" --reference-doc="[Ruta_a_resources]/reference-arial.odt" -o "[Fitxer_Sortida].odt"
    ```

## Millores Recents
*   **Lletra Arial:** S'utilitza un document de referència personalitzat per forçar la lletra Arial a tot el document.
*   **Imatges Centrades:** El filtre de Lua ara assigna els estils `Figure` i `Caption` a les imatges i peus de foto, els quals estan configurats per estar centrats al document de referència.

## Característiques del Filtre
*   **Eliminació de Scripts:** Detecta i elimina automàticament els blocs `<script>` (MathJax).
*   **Extracció d'Imatges:** Processa els `<div>` de la IOC per extreure la imatge i el peu de foto (`<em>`).
*   **Format de Sortida:** Insereix la imatge amb l'estil `Figure` i el peu de foto amb l'estil `Caption` per garantir l'alineació centrada i el format correcte a l'ODT.

## Exemple d'Ús
> Usuari: "Exporta aquest fitxer a ODT utilitzant la skill"
> Agent: (Executa Pandoc amb el filtre Lua i la referència Arial) "Fitxer convertit a ODT amb lletra Arial i imatges centrades."

## Ús
Quan l'usuari demani "convertir a ODT" o "exportar a Word/ODT", utilitza aquesta skill per automatitzar el procés de forma ràpida.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sergihorrillo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
