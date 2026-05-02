---
name: vbaexcel
description: Extraer y reimportar codigo VBA de archivos Excel .xlsm en Windows usando Python (pywin32/oletools) o VBScript. Usar para exportar modulos .bas, refactorizar en VS Code y volver a importar al XLSM. Use when this capability is needed.
metadata:
  author: lunasoft2001
---

# vbaExcel

## Uso rapido

1. Extraer VBA a archivos .bas.
2. Refactorizar los .bas.
3. Reimportar al .xlsm.

## Flujo recomendado

### 1) Exportar VBA

- Ejecuta el script de exportacion en `scripts/export_vba.py`.
- Si falla el acceso a VBA, habilita `AccessVBOM` con `scripts/enable_vba_access.reg`.

### 2) Refactorizar

- Edita los archivos .bas en VS Code (o en el editor VBA).

### 3) Reimportar

- Ejecuta el script `scripts/import_vba.py` para reemplazar el codigo de cada modulo.

## Notas importantes

- Excel debe estar instalado.
- Cierra Excel antes de exportar o importar.
- Siempre crea un backup del XLSM antes de importar.

## Scripts incluidos

- `scripts/export_vba.py`: exporta VBA a .bas usando VBScript y COM.
- `scripts/import_vba.py`: importa los .bas al XLSM via COM.
- `scripts/enable_vba_access.reg`: habilita acceso programatico a VBA.

## Cuando usar este skill

- Quieres extraer VBA para refactorizar.
- Quieres automatizar el reingreso del codigo VBA al XLSM.
- Tienes bloqueado el acceso a VBProject y necesitas habilitarlo.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lunasoft2001) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
