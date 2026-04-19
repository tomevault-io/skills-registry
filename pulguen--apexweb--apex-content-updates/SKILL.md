---
name: apex-content-updates
description: Gestionar actualizaciones de contenido operativo y comercial del sitio Apex en componentes React existentes. Usar cuando se solicite cambiar entrenadores, horarios, cupos, precios, ubicacion, enlaces de contacto o textos visibles sin redisenar arquitectura ni migrar tecnologia. Use when this capability is needed.
metadata:
  author: pulguen
---

# Apex Content Updates

## Objetivo

Actualizar contenido visible del sitio sin alterar la estructura funcional del frontend.

## Ejecutar este flujo

1. Identificar el componente origen del dato.
2. Editar primero estructuras de datos (`coaches`, `PLANES`, strings constantes) antes de tocar markup.
3. Mantener formato local y reglas actuales:
- Renderizar precios con `toLocaleString('es-AR')`.
- Mantener enlaces WhatsApp como `https://wa.me/<numero>`.
- Conservar claves estables en listas (`id`, `key`).
4. Preservar clases CSS existentes para evitar regresiones visuales.
5. Limitar cambios al alcance del pedido; no introducir nuevo estado salvo necesidad real.

## Mapa de archivos

- `src/Components/Contact/Contact.jsx`: entrenadores, horarios, cupos, botones de contacto.
- `src/Components/Tafiras/Tarifas.jsx`: planes, precios, notas de pago.
- `src/Components/Location/Location.jsx`: direccion, texto y mapa embebido.
- `src/App.js`: orden de secciones.

## Checklist de salida

- Confirmar que no se rompieron listas ni colapsables.
- Confirmar que el contenido nuevo coincide con el pedido literal.
- Dejar `TODO` breve solo si falta un dato bloqueante.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pulguen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
