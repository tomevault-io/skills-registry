---
name: ehu-bilatu-phone
description: Skill para buscar y extraer números de teléfono y correos desde el buscador 'BILATU' de UPV/EHU (ehu.eus/bilatu). Use when this capability is needed.
metadata:
  author: juananpe
---

# Skill Instructions

Esta skill ayuda a localizar y extraer información de contacto (teléfono y correo) de personas en el buscador BILATU (https://www.ehu.eus/bilatu).

## Cuándo usar
- Cuando se pida: "buscar número de teléfono de <Nombre>", "dame el teléfono de <Apellido>", o consultas similares que impliquen buscar contactos en UPV/EHU.

## Qué debe hacer el agente
1. Ir a `https://www.ehu.eus/bilatu/buscar/bilatu.php?lang=es`.
2. Rellenar el campo de búsqueda con el nombre o apellidos (preferiblemente `Apellidos/ Nombre` o `Primer apellido` + `Segundo apellido`).
3. Hacer clic en `BUSCAR` y esperar los resultados.
4. Si aparece un resultado, hacer clic en el nombre para abrir la ficha completa.
5. Extraer y devolver de forma estructurada: `nombre`, `departamento`, `campus`, `teléfono` (formato limpio), `correo_electrónico`.
6. Si no se encuentra resultado, devolver `{ found: false }`.

## Plantillas de prompt (ejemplos)
- "Busca en BILATU el teléfono de `Pereira Varela` y devuélvelo como `{"found":true,"telefono":"943015290","email":"juanan.pereira@ehu.eus"}`."
- "Usa la skill `ehu-bilatu-phone` para extraer el teléfono y correo de `Juan Antonio Pereira Varela`".

## Scripts incluidos
- `scripts/search_ehu_bilatu_phone.js`: script Node + Playwright que automatiza la búsqueda y devuelve JSON. Ejecutable localmente para reproducir la extracción.

## Dependencias y uso
- Requiere Node.js y Playwright instalado. Para probar localmente:

  1. npm i -D playwright
  2. Ejecutar la descarga de navegadores de Playwright (necesario una sola vez):
     - `npx playwright install --with-deps` (recomendado) o `npx playwright install` en macOS/Linux.
  3. Ejecutar el script:
     - `node .github/skills/ehu-bilatu-phone/scripts/search_ehu_bilatu_phone.js "Pereira Varela"`

- Nota: si el script devuelve `{ "found": false, "error": "..." }`, verifica que Playwright y los navegadores estén instalados y que la red permita descargar los binarios.


## Notas de fiabilidad
- El buscador es estable pero puede cambiar su HTML; el script y las instrucciones intentan manejar los campos `abi_ize`, `abi1`, `abi2` y `bidali` (submit).
- Cambios recientes en el script:
  - Se ha corregido el acceso inseguro a coincidencias de correo para evitar excepciones cuando no hay match (devuelve `correo_electronico: null`).
  - Ahora extrae también `departamento` y `campus` cuando están disponibles en la ficha.
  - Mejora en la detección del nombre para evitar tomar líneas equivocadas.
  - En caso de error inesperado, el script devuelve `{ found: false, error: "mensaje" }` en lugar de lanzar una excepción.
- Si la web cambia, actualizar los selectores en `scripts/search_ehu_bilatu_phone.js`.

---

# Examples

- Ejemplo de entrada: `Pereira Varela` → Ejemplo de salida: `{ "found": true, "nombre": "PEREIRA VARELA, JUAN ANTONIO", "telefono": "943015290", "email": "juanan.pereira@ehu.eus" }`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/juananpe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
