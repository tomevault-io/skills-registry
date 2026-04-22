---
name: coolify-architect-guidance
description: Proporciona orientación arquitectónica completa para Coolify basada en directo_coolify.md, incluyendo modelo de referencia, reglas de diseño, criterios de éxito y principios de operación push-to-deploy. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Orientación Arquitectónica para Coolify

## Propósito

Este skill te guía en la construcción de **arquitecturas de despliegue reproducibles** en Coolify, partiendo de la metodología documentada en `directo_coolify.md`. Asegura que cada solución que generes siga el principio: **"push a GitHub y despliegue automático en Coolify a la primera"** sin intervención manual recurrente.

---

## 1. Modelo de Referencia: Coolify + Docker Compose

### Arquitectura Objetivo

```
┌─────────────────────────────────────────────────────────┐
│ GitHub Repository (Source of Truth)                     │
│ ├── docker-compose.yml (base, Coolify-ready)           │
│ ├── .github/workflows/ (CI/CD triggers)                │
│ └── Dockerfile(s) y código                             │
└──────────────────────┬──────────────────────────────────┘
                       │ push + tag
                       ▼
┌─────────────────────────────────────────────────────────┐
│ Coolify Panel (217.216.81.73:8000)                      │
│ ├── Detecta cambios en GitHub (webhook)                │
│ ├── Build remoto (docker build)                        │
│ └── Deploy automático                                  │
└──────────────────────┬──────────────────────────────────┘
                       │ enrutamiento
                       ▼
┌─────────────────────────────────────────────────────────┐
│ Traefik (gestionado por Coolify)                        │
│ ├── Routing HTTP/HTTPS                                 │
│ ├── TLS (Cloudflare origins)                           │
│ └── Reverse proxy a contenedores                       │
└──────────────────────┬──────────────────────────────────┘
                       │ local network
                       ▼
┌─────────────────────────────────────────────────────────┐
│ Contenedores Docker (stacks multi-servicio)            │
│ ├── web (frontend)          expose: 8080               │
│ ├── api (backend)           expose: 3000               │
│ ├── db (PostgreSQL)         expose: 5432 (solo interno)│
│ ├── cache (Redis)           expose: 6379 (solo interno)│
│ └── worker (async tasks)    expose: N/A                │
│                                                         │
│ Volúmenes persistentes: db_data, uploads, config       │
└─────────────────────────────────────────────────────────┘

                       ▲
                       │ flujo público
                       │
┌─────────────────────────────────────────────────────────┐
│ Cloudflare (DNS + Proxy)                                │
│ ├── portal.da-tica.com → 217.216.81.73                 │
│ ├── api.da-tica.com → 217.216.81.73                    │
│ └── HTTPS, DDoS, cache                                 │
└─────────────────────────────────────────────────────────┘
                       ▲
                       │
                    Internet
```

### Responsabilidades por Capa

| Capa | Responsable | Qué Gestiona |
|------|-------------|--------------|
| **GitHub** | Dev (tú) | Código fuente, versionado, triggers |
| **Coolify** | DevOps (automático) | Build, despliegue, reintentos, logs |
| **Traefik** | Coolify (automático) | Enrutamiento, TLS, certificados |
| **Docker** | Código (Dockerfile) | Construcción de imagen, runtime |
| **Volumes** | DevOps (persistencia) | Datos, config, uploads (nunca se pierden) |

---

## 2. Reglas de Diseño para `docker-compose.yml`

### Principio Fundamental

> **Un único `docker-compose.yml` base, válido para Coolify sin overrides locales.**

### Estructura Obligatoria

```yaml
version: "3.8"

services:
  # Servicios ordenados por dependencia (DB → App → Workers)
  db:
    image: postgres:16-alpine              # ✅ Versión fija, alpine
    restart: unless-stopped                # ✅ Reinicio automático
    expose:
      - "5432"                             # ✅ Solo expose (no ports)
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}    # ✅ Variable, no hardcoded
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - db_data:/var/lib/postgresql/data   # ✅ Volumen persistente
    healthcheck:                           # ✅ Healthcheck para dependencias
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - backend

  api:
    image: your-org/api:${API_TAG}         # ✅ Tag dinámico desde env
    restart: unless-stopped
    expose:
      - "3000"
    environment:
      NODE_ENV: production                 # ✅ Ambiente explícito
      DB_HOST: db                          # ✅ Nombre de servicio
      DB_PORT: 5432
      DB_USER: postgres
      DB_PASSWORD: ${DB_PASSWORD}
      DB_NAME: ${DB_NAME}
    volumes:
      - app_logs:/app/logs                 # ✅ Logs persistentes
      - app_uploads:/app/uploads
    depends_on:
      db:
        condition: service_healthy         # ✅ Espera a que DB esté lista
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:3000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
    networks:
      - backend
    deploy:
      resources:
        limits:
          memory: 512M                     # ✅ Límites si VPS es pequeño

  web:
    image: your-org/web:${WEB_TAG}
    restart: unless-stopped
    expose:
      - "8080"
    environment:
      REACT_APP_API_URL: ${API_URL}        # ✅ URL dinámica
      PORT: 8080                           # ✅ App escucha en 0.0.0.0
    depends_on:
      api:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://127.0.0.1:8080/"]
      interval: 30s
      timeout: 5s
      retries: 3
    networks:
      - backend

volumes:
  db_data:                                 # ✅ Volumen nombrado
  app_logs:
  app_uploads:

networks:
  backend:
    driver: bridge
```

### Reglas Clave por Sección

#### **image:**
- ✅ Siempre versión fija: `postgres:16-alpine`, `node:18.20.1-alpine3.18`
- ✅ Preferir `alpine` (menor tamaño, menor superficie de ataque)
- ❌ NUNCA `latest` o sin versión

#### **ports vs expose:**
- ❌ `ports: "8080:8080"` → Expone al host (Traefik gestiona enrutamiento)
- ✅ `expose: ["8080"]` → Solo documentación + disponible en red Docker
- Excepción: Solo en **modo local** con Traefik local

#### **environment:**
- ✅ Variables como `${VAR}` → leídas de `.env` o Coolify
- ✅ Agrupar por categoría (DATABASE, APPLICATION, SECURITY)
- ❌ NUNCA valores hardcodeados (passwords, API keys, tokens)

#### **volumes:**
- ✅ Volúmenes nombrados para persistencia: `db_data:/var/lib/postgresql/data`
- ✅ Nunca bind mounts a rutas locales (rompe portabilidad)
- ✅ Documentar qué datos se persisten

#### **healthcheck:**
- ✅ Obligatorio en BD, APIs, servicios críticos
- ✅ Debe usar comandos disponibles en la imagen (ej. `pg_isready` en postgres)
- ✅ Incluir `start_period` para apps lentas en startup

#### **depends_on:**
- ✅ Usar `condition: service_healthy` si es crítico
- ✅ Permite que Coolify coordine arranque
- ❌ No confiar solo en `depends_on` sin healthcheck

#### **restart:**
- ✅ `restart: unless-stopped` → Reinicia siempre salvo parada manual
- ❌ NUNCA omitir o usar `no`

#### **networks:**
- ✅ Usar redes Docker nombradas (`backend`, `frontend`)
- ❌ NUNCA `network_mode: host`
- ✅ Servicios internos en red privada

---

## 3. Criterios Verificables de Éxito Post-Despliegue

Una vez que Coolify haya desplegado tu stack, verifica que cumple:

### Checklist de Disponibilidad

- [ ] **Dominio público funciona:** `curl -I https://portal.da-tica.com` → 200 OK
- [ ] **Subdominio específico funciona:** `curl -I https://api.da-tica.com` → 200 OK
- [ ] **TLS activo:** `openssl s_client -connect portal.da-tica.com:443` muestra certificado válido
- [ ] **Redirección HTTP → HTTPS:** `curl -I http://portal.da-tica.com` → 301 a HTTPS

### Checklist de Servicios

- [ ] **Todos los servicios en estado "running":** `docker ps` o panel Coolify
- [ ] **Sin reinicios repetidos:** logs muestran arranques limpios, no loops de restart
- [ ] **Healthchecks pasan:** estado "healthy" en contenedores con healthcheck
- [ ] **Comunicación interna:** servicio `api` puede acceder a `db:5432` sin errores

### Checklist de Persistencia

- [ ] **Volúmenes creados:** `docker volume ls` muestra `db_data`, `app_logs`, `app_uploads`
- [ ] **Datos persistentes tras redeploy:** redeploy no pierde datos
- [ ] **Datos persistentes tras restart:** `docker restart` no pierde datos
- [ ] **Backup verificado:** si existe política de backup, confirmar ejecución reciente

### Checklist de Versión

- [ ] **Tag correcto desplegado:** en Coolify panel o `docker inspect --format='{{.Image}}'` muestra versión esperada
- [ ] **Trazabilidad:** GitHub tag coincide con imagen en producción
- [ ] **Rollback posible:** versión anterior accesible si es necesario

---

## 4. Operación: GitHub → Coolify "Sin Tocar Coolify"

### Flujo Operativo (8 pasos)

```
1. Dev realiza cambios locales
   ↓
2. Push a GitHub (rama + commit + tag)
   ↓
3. GitHub webhook → Coolify detecta cambio
   ↓
4. Coolify inicia build remoto (docker build)
   ↓
5. Build exitoso → imagen almacenada
   ↓
6. Deploy automático → stack se actualiza
   ↓
7. Healthchecks verifican servicios
   ↓
8. Disponible en producción (sin intervención manual)
```

### Versionado en GitHub

#### Tags de Release

Usar **semantic versioning** (SemVer):

```bash
# Primera release
git tag v1.0.0
git push origin v1.0.0

# Parche (bug fix)
git tag v1.0.1
git push origin v1.0.1

# Feature minor
git tag v1.1.0
git push origin v1.1.0

# Breaking change
git tag v2.0.0
git push origin v2.0.0
```

#### En `docker-compose.yml`

```yaml
services:
  api:
    image: your-org/api:${API_TAG:-latest}  # ← Coolify inyecta API_TAG
```

En Coolify, definir variable: `API_TAG=v1.0.0` (actualizar cada release).

### Checklist Previo al Push (Mínimo 12)

**Antes de hacer `git push`:**

1. [ ] ✅ `docker-compose config` valida sintaxis YAML sin errores
2. [ ] ✅ No hay `ports:` en servicios web (solo `expose:`)
3. [ ] ✅ No hay credenciales hardcodeadas (grep `PASSWORD`, `API_KEY`, `SECRET`)
4. [ ] ✅ Todas las imágenes tienen versión fija (no `latest`)
5. [ ] ✅ Todas las imágenes usan `alpine` cuando está disponible
6. [ ] ✅ `restart: unless-stopped` presente en todos los servicios
7. [ ] ✅ Volúmenes persistentes definidos (BD, uploads, config)
8. [ ] ✅ Healthchecks presentes en servicios críticos (DB, API)
9. [ ] ✅ `depends_on` con `condition: service_healthy` donde aplique
10. [ ] ✅ Variables de entorno documentadas en `.env.example`
11. [ ] ✅ `.env` local funciona (`docker-compose up -d`)
12. [ ] ✅ Tag Git seguido (ej. `v1.0.0`) antes de push final

### Checklist Post-Deploy (Mínimo 8)

**Después que Coolify haya desplegado:**

1. [ ] ✅ Dominio público responde (HTTP 200)
2. [ ] ✅ TLS activo (HTTPS con certificado válido)
3. [ ] ✅ Todos los servicios en estado "running"
4. [ ] ✅ Sin reinicios en loop (logs limpios)
5. [ ] ✅ Healthchecks pasan (estado "healthy")
6. [ ] ✅ Persistencia verificada (redeploy no pierde datos)
7. [ ] ✅ Trazabilidad de versión (tag en imagen coincide con GitHub)
8. [ ] ✅ Rollback probado (versión anterior accessible si es necesario)

---

## 5. Principios Operacionales

### "Push a GitHub, Despliegue Automático"

1. **Toda configuración versionada:** `docker-compose.yml`, Dockerfile, `.env.example`
2. **Sin intervención manual:** Coolify webhook detecta cambios automáticamente
3. **Variables por entorno:** `${VAR}` en compose, valores en Coolify o `.env`
4. **Validación local primero:** si funciona localmente, funciona en Coolify

### Excepciones Tipificadas

Si un despliegue falla, diagnóstico rápido:

- **Build falla:** revisar logs de Docker, contextos en Dockerfile
- **Servicio no inicia:** revisar healthcheck, puerto correcto, variable faltante
- **Datos perdidos:** verificar volumen existente, no recreado
- **Timeout:** aumentar `start_period` en healthcheck o `depends_on` timeout

### Responsabilidad Compartida

| Quién | Qué | Cuándo |
|------|-----|--------|
| **Dev** | Código + Dockerfile + compose | En cada cambio |
| **Coolify** | Build + deploy + monitoring | Automático (webhook) |
| **Ops** | Variables en Coolify + backups | Setup inicial + monitoreo |

---

## 6. Próximos Pasos al Aplicar Este Skill

1. **Revisar tu `docker-compose.yml` actual** con skill `validate-coolify-compose`
2. **Identificar errores comunes** con skill `validate-common-coolify-errors`
3. **Preparar flujo GitHub → Coolify** con skill `coolify-github-workflow`
4. **Ejecutar checklist pre-deploy** antes de todo push

---

## 7. Referencia Rápida: Estructura Mínima Viable

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - db_data:/var/lib/postgresql/data
    expose:
      - "5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
  
  app:
    image: myapp:${APP_TAG}
    restart: unless-stopped
    expose:
      - "8080"
    environment:
      DB_HOST: postgres
      DB_PASSWORD: ${DB_PASSWORD}
    depends_on:
      postgres:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:8080/health"]
      interval: 30s
      timeout: 5s
      retries: 3

volumes:
  db_data:
```

**Esto es suficiente para empezar. Expande según necesidades.**

---

## Notas Finales

- Este skill es **complementario** a los otros tres (validate-coolify-rules, validate-coolify-compose, coolify-deploy-checklist).
- Si algo no está claro, pide **referencia específica a directo_coolify.md**.
- Recuerda: **"Si funciona en local bajo Traefik, funcionará en Coolify sin sorpresas."**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
