---
name: preparar-paquete-auditoria
description: > Use when this capability is needed.
metadata:
  author: hecpac
---

# Objetivo
Armar un paquete de evidencia auditable y navegable, priorizando trazabilidad y enlaces a fuentes del repo.

# Cuando usar / Cuando NO usar
- Usar para preparar evidencia de auditoria interna o externa.
- Usar cuando se solicite consolidar documentos, registros y soportes por proceso.
- NO usar para redaccion aislada de documentos sin empaquetado.
- NO usar para analisis teoricos sin entrega de paquete.

# Flujo paso a paso
1. Ejecutar `sgc-build-indexes` para sincronizar LMD y matriz.
2. Crear carpeta con fecha actual: `audit_packages/YYYY-MM-DD/`.
3. Generar `audit_packages/YYYY-MM-DD/README.md` con indice y enlaces/rutas a evidencias.
4. Incluir enlaces a:
- `docs/_control/lmd.yml`.
- `docs/_control/matriz_registros.yml`.
- Documentos `VIGENTE` y documentos clave `BORRADOR` si el usuario lo pide.
- Registros clave por proceso.
- Evidencia de KPIs, auditorias, NC y CAPA (si existe).
5. Organizar por proceso para facilitar muestreo.
6. Si falta evidencia, registrar `TODO: <dato>` en el README.

# Estructura sugerida del README del paquete
- Objetivo y alcance del paquete.
- Indice por proceso.
- Documentos vigentes relevantes.
- Registros/evidencias por proceso.
- KPIs y analisis de desempeno.
- Auditorias internas/externas.
- No conformidades y CAPA.
- Pendientes o brechas (`TODO`).

# Reglas
- No copiar informacion sensible si no se indico expresamente.
- Preferir enlaces/rutas a documentos fuente antes que duplicar contenido.
- No inventar evidencias; usar placeholders `TODO: <dato>` cuando falten.

# Definicion de Terminado (DoD)
- Carpeta `audit_packages/YYYY-MM-DD/` creada.
- `README.md` generado con indice y enlaces funcionales.
- Incluidos `lmd.yml`, `matriz_registros.yml` y lista minima de evidencias por proceso (si existen) o placeholders.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hecpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
