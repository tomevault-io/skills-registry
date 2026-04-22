---
name: validate-coolify-compose
description: Valida que un docker-compose.yml cumpla con todas las reglas obligatorias de Coolify (sin ports en web, sin network_mode host, credenciales en variables, versiones fijas, restart policies, healthchecks, expose definido, volúmenes persistentes, etc.) Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Validar docker-compose para Coolify

## Propósito

Este skill te ayuda a asegurar que cualquier `docker-compose.yml` que generes o modifiques cumple con las reglas obligatorias del entorno Coolify de producción (VPS Contabo + Cloudflare). Evita errores comunes que causarían fallos en despliegue.

## Reglas que valida

### 🚫 Prohibiciones Absolutas (FAIL si aparecen)

1. **Puertos expuestos en servicios web:** No `ports:` (ej. `"80:80"`, `"8080:8080"`) en servicios que serán enrutados por Traefik/Coolify.
   - ✅ Correcto: usar `expose:` en lugar de `ports:`
   - ❌ Incorrecto: `ports: ["8080:8080"]`

2. **`network_mode: host`:** Nunca usar, rompe el aislamiento de contenedores.

3. **Credenciales hardcodeadas:** No valores sensibles en claro (passwords, API keys, tokens).
   - ✅ Correcto: `POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}`
   - ❌ Incorrecto: `POSTGRES_PASSWORD: mipassword123`

4. **Imágenes sin versión fija:** No usar `latest` o tags flotantes.
   - ✅ Correcto: `postgres:16-alpine`, `node:18.20.1-alpine3.18`
   - ❌ Incorrecto: `postgres:latest`, `node:alpine`

5. **Falta de `restart: unless-stopped`:** Obligatorio en producción para recuperación ante fallos.

### ✅ Requisitos Obligatorios (WARN si faltan)

1. **`expose:` o puerto documentado:** Cada servicio debe indicar dónde escucha (preferir `expose:` sobre `ports:`).

2. **Apps escuchan en `0.0.0.0`:** No solo en `localhost` o `127.0.0.1`.

3. **`healthcheck`** en servicios críticos (DB, API, cache).

4. **`depends_on` con `condition: service_healthy`** si hay dependencias.

5. **Volúmenes persistentes** para datos (BD, uploads, config).

6. **Variables de entorno** bien organizadas y documentadas.

7. **Usuario no-root** (`user: non-root`) si la imagen lo soporta.

8. **Imágenes `alpine`** cuando esté disponible (más livianas).

## Cómo usar este skill

### Paso 1: Recopila el archivo

Proporciona el archivo `docker-compose.yml` que deseas validar (completo o el servicio específico).

### Paso 2: Análisis línea por línea

Verifico cada regla:

```yaml
# Ejemplo de FALLO
services:
  web:
    image: node:latest                    # ❌ FAIL: sin versión fija
    ports: ["8080:8080"]                  # ❌ FAIL: ports en web
    restart: always                       # ⚠️ WARN: debería ser unless-stopped
    environment:
      - DB_PASSWORD=abc123                # ❌ FAIL: credencial en claro
```

```yaml
# Ejemplo CORRECTO
services:
  web:
    image: node:18.20.1-alpine3.18        # ✅ versión fija, alpine
    restart: unless-stopped               # ✅ restart correcto
    expose:
      - "8080"                            # ✅ expose en lugar de ports
    environment:
      - DB_PASSWORD=${DB_PASSWORD}        # ✅ variable de entorno
    healthcheck:
      test: ["CMD", "wget", "-q", "http://127.0.0.1:8080"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s                   # ✅ healthcheck presente
    user: node                            # ✅ usuario no-root
    volumes:
      - app_data:/home/node/app           # ✅ volumen persistente
```

### Paso 3: Reporte

Te presento un **reporte estructurado**:

| Regla | Estado | Detalle |
|-------|--------|---------|
| Sin `ports:` en web | ✅ PASS | No hay exposición directa de puertos |
| Sin `network_mode: host` | ✅ PASS | Aislamiento OK |
| Versiones fijas | ⚠️ WARN | `postgres:16-alpine` OK, pero `redis` sin versión |
| Credenciales | ❌ FAIL | Línea 45: `API_KEY=xxxxx` en claro |
| `restart: unless-stopped` | ✅ PASS | Todos los servicios OK |
| `expose:` definido | ✅ PASS | Puertos internos documentados |
| `healthcheck` | ⚠️ WARN | Falta en servicio `cache` |
| Volúmenes persistentes | ✅ PASS | `postgres_data`, `app_storage` OK |

### Paso 4: Recomendaciones

Si hay fallos o warnings, te doy **instrucciones específicas** para arreglarlo:

```markdown
**FALLO #1:** Línea 45 - Credencial en claro
Cambiar:
  API_KEY=sk-1234567890
Por:
  API_KEY=${API_KEY}

Luego, en tu archivo `.env` o en Coolify, definir:
  API_KEY=sk-1234567890
```

## Validación final

Al terminar, respondo: **¿Este compose está listo para Coolify?**

- ✅ **SÍ, listo para producción** (0 FAILs, 0 WARNs críticos)
- ⚠️ **CON WARNINGS** (0 FAILs, algunos WARNs que no rompen, pero mejora recomendada)
- ❌ **NO, hay FAILs críticos** (lista de correcciones obligatorias)

## Integración con otros skills

- **Luego de validar**, usa `coolify-deploy-checklist` para el checklist final pre-despliegue.
- **Si necesitas crear** un compose desde cero, usa primero `validate-coolify-rules` para entender los constraints.

## Notas

- Este skill **no modifica** el archivo automáticamente. Eres tú quien decides los cambios.
- Si un error no está claro, pregunto por más contexto (ej. "¿este es un servicio web o interno?").
- Todas las reglas aplican a **stacks de producción** en Coolify. Para local (`docker-compose.coolify-local.yml`), hay excepciones (ej. `ports:` está permitido para Traefik local).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
