---
name: coolify-github-workflow
description: Define el flujo operativo paso a paso desde commit hasta despliegue automático en Coolify, incluyendo versionado, checklists previos y post-deploy. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Flujo GitHub → Coolify "Push-to-Deploy"

## Propósito

Establecer un **proceso operativo reproducible** que permita:
1. Commitear cambios a GitHub
2. Coolify construya y desplegue **automáticamente**
3. Minimizar intervención manual
4. Garantizar trazabilidad de versiones

---

## Flujo Operativo (Paso a Paso)

### FASE 1: Preparación Local (ANTES del push)

#### Paso 1.1: Validar Código y Configuración Local

Antes de hacer commit, ejecutar:

```bash
# Validar sintaxis YAML del compose
docker-compose config > /dev/null && echo "✅ Compose válido"

# Buscar credenciales hardcodeadas
grep -r "PASSWORD\|API_KEY\|SECRET\|token" docker-compose.yml \
  | grep -v "\${" && echo "⚠️ Credenciales detectadas" || echo "✅ Sin credenciales en claro"

# Verificar tags de imágenes (no latest)
grep -i ":latest" docker-compose.yml && echo "⚠️ Imágenes sin versión" || echo "✅ Versiones fijas"

# Verificar puertos en servicios web
grep -A 5 "services:" docker-compose.yml | grep "ports:" && echo "⚠️ Ports expuestos" || echo "✅ Sin ports en web"
```

#### Paso 1.2: Aplicar Checklist Pre-Push (Mínimo 12 validaciones)

- [ ] `docker-compose config` ejecuta sin errores
- [ ] No hay credenciales en `docker-compose.yml` (usar `${VAR}`)
- [ ] Todas las imágenes tienen versiones fijas (no `latest`)
- [ ] Servicios web usan `expose:` en lugar de `ports:`
- [ ] BD y servicios internos NO tienen puertos expuestos
- [ ] `restart: unless-stopped` en todos los servicios de producción
- [ ] Volúmenes persistentes definidos para servicios con estado
- [ ] Healthchecks en servicios críticos (DB, API)
- [ ] `depends_on` con `condition: service_healthy` si hay dependencias
- [ ] Variables de entorno documentadas (crear `.env.example`)
- [ ] `.gitignore` contiene `.env` y archivos sensibles
- [ ] README actualizado con instrucciones de despliegue

**Ejemplo `.env.example`:**
```env
# Base de datos
POSTGRES_PASSWORD=your_secure_password_here
POSTGRES_USER=postgres
POSTGRES_DB=myapp_db

# Aplicación
PORT=8080
NODE_ENV=production
LOG_LEVEL=info

# API externas
API_KEY=your_api_key_here
```

#### Paso 1.3: Prueba Local con Traefik (Opcional pero Recomendado)

```bash
# Usar docker-compose.coolify-local.yml si existe
docker-compose -f docker-compose.coolify-local.yml up -d

# Esperar health
sleep 10

# Probar acceso
curl http://localhost:8081  # O con dominio local

# Ver logs
docker-compose logs -f web

# Detener
docker-compose -f docker-compose.coolify-local.yml down
```

---

### FASE 2: Versionado y Commit a GitHub

#### Paso 2.1: Estructura de Repositorio

```
repo-root/
├── docker-compose.yml          # Producción (Coolify)
├── docker-compose.coolify-local.yml  # Local con Traefik (opcional)
├── .env.example                # Plantilla de variables
├── .gitignore                  # ✅ Incluye .env, secrets, node_modules
├── README.md                   # Instrucciones de despliegue
├── Dockerfile                  # Si es multi-stage
├── traefik/
│   └── dynamic.yml            # Config dinámica Traefik (si aplica)
└── src/
    └── ... (código de aplicación)
```

#### Paso 2.2: Commit con Mensaje Estructurado

Usar **conventional commits** para claridad:

```bash
git add .
git commit -m "feat: añadir servicio de cache Redis" \
  -m "- Agrega Redis 7.0 con volumen persistente
- Configura healthcheck con redis-cli
- Documenta variable REDIS_URL en .env.example"

git push origin feature/redis-cache
```

#### Paso 2.3: Versionado con Tags (Release)

Cuando esté listo para producción:

```bash
# Tag con versión semántica
git tag -a v1.2.3 -m "Release: añadir soporte Redis y optimizar DB queries"

# Push tag a remoto
git push origin v1.2.3

# Verificar tag fue creado
git describe --tags
```

**Convención de versiones:**
- `v1.2.3` = major.minor.patch
- Mayor: cambios incompatibles o despliegues críticos
- Menor: nuevas features o mejoras
- Patch: bugfixes o ajustes menores

---

### FASE 3: Configuración en Coolify (Una sola vez por servicio)

#### Paso 3.1: Crear Proyecto en Coolify

1. Acceder a Coolify: `http://217.216.81.73:8000`
2. Crear nuevo proyecto o servicio
3. Conectar repositorio GitHub:
   - URL: `https://github.com/tu-org/tu-repo.git`
   - Rama: `main` (o la que uses por defecto)
   - Activar despliegue automático en push

#### Paso 3.2: Configurar Variables de Entorno

En Coolify, agregar **cada variable** de `.env.example`:

```
POSTGRES_PASSWORD = [valor real seguro]
POSTGRES_USER = postgres
POSTGRES_DB = myapp_db
PORT = 8080
NODE_ENV = production
API_KEY = [valor real]
...
```

**Nota:** Coolify **no** debe leer `.env` del repo (ese es solo para local). Las variables se configuren en Coolify UI.

#### Paso 3.3: Configurar Dominio y TLS

- Dominio: `api.da-tica.com` (o el que corresponda)
- Cloudflare: apuntar subdominio a `217.216.81.73`
- Coolify gestiona automáticamente el certificado SSL

#### Paso 3.4: Habilitación de Webhook (Despliegue Automático)

Coolify debe tener habilitado el webhook de GitHub para:
- Detectar pushes a `main` o rama configurada
- Construir imagen automáticamente
- Desplegar sin intervención manual

---

### FASE 4: Push a GitHub → Despliegue Automático

#### Paso 4.1: Flujo Automático en Coolify

```
1. Push a GitHub (git push origin main)
   ↓
2. GitHub envía webhook a Coolify
   ↓
3. Coolify detecta cambio y comienza build
   ↓
4. Docker construye imagen con contexto del repo
   ↓
5. Coolify ejecuta docker-compose con las variables configuradas
   ↓
6. Traefik detecta nuevos servicios y los enruta
   ↓
7. Healthchecks validan que todo esté saludable
   ↓
8. Servicio está disponible públicamente por dominio
```

#### Paso 4.2: Monitoreo Durante Deploy

```bash
# En Coolify UI:
# - Ver "Recent Deployments"
# - Expandir el deployment y ver logs
# - Esperar estado "Success" o "Running"

# Desde terminal (si tienes SSH):
ssh root@217.216.81.73
docker ps  # Ver contenedores en ejecución
docker logs <container_id>  # Ver logs del servicio
```

---

### FASE 5: Validación Post-Deploy (Checklist: Mínimo 8)

Después de desplegar, verificar **obligatoriamente**:

- [ ] Servicio accesible públicamente por dominio (ej. `curl https://api.da-tica.com/health`)
- [ ] TLS activo y válido (certificado mostrado en navegador)
- [ ] Status codes correctos (200, 201, etc.; no 500, 502, 503)
- [ ] Servicios en estado "Running" (sin "Restarting" o "Unhealthy")
- [ ] Healthchecks pasando en Coolify (`healthy` status)
- [ ] Persistencia confirmada (ej. si BD tiene datos, verificar que sigue ahí)
- [ ] Versión desplegada es la esperada (en logs o endpoint `/version` si existe)
- [ ] No hay errores críticos en logs de aplicación

**Script de validación rápida:**

```bash
DOMAIN="api.da-tica.com"

echo "🔍 Validando despliegue en $DOMAIN..."

# 1. Disponibilidad
curl -I https://$DOMAIN && echo "✅ Dominio accesible" || echo "❌ Dominio inaccesible"

# 2. TLS
curl -I https://$DOMAIN 2>&1 | grep -i "ssl\|tls" && echo "✅ TLS activo" || echo "⚠️ Verificar TLS"

# 3. Healthcheck (si existe endpoint)
curl -s https://$DOMAIN/health | jq . && echo "✅ Health OK" || echo "⚠️ Revisar health"

# 4. Versión (si existe)
curl -s https://$DOMAIN/version && echo "✅ Versión detectada" || echo "⚠️ Sin endpoint version"
```

---

## Casos de Excepción y Resolución

### Excepción 1: Despliegue Fallido (Coolify muestra error)

**Síntomas:**
- Estado "Failed" en Coolify
- Logs muestran error en build o startup

**Resolución rápida:**

```bash
# En Coolify, ver los logs del último deployment
# Buscar patrones comunes:

# Imagen no encontrada → verificar nombre y versión
grep "image:" docker-compose.yml

# Variable faltante → verificar que está en Coolify UI
docker-compose config  # Ver valores interpolados

# Puerto ya en uso → cambiar puerto interno en compose

# Volumen corrupto → en Coolify, borrar volumen y redeploy
```

### Excepción 2: Servicio Arranca Pero No Responde

**Síntomas:**
- Contenedor está "Running" pero curl devuelve timeout o 500

**Resolución:**

```bash
# SSH a VPS
ssh root@217.216.81.73

# Inspeccionar logs del contenedor
docker logs <container_id> -f

# Verificar que la app escucha en 0.0.0.0, no localhost
docker exec <container_id> netstat -tlnp | grep LISTEN

# Si escucha en 127.0.0.1, el problema es el Dockerfile
# Corrección: agregar -bind 0.0.0.0 o configurar en env
```

### Excepción 3: Datos Persistentes Perdidos Tras Redeploy

**Síntomas:**
- BD está vacía después de hacer redeploy
- Volúmenes no están vinculados correctamente

**Verificación:**

```bash
# En Coolify, ver que volúmenes están definidos
docker-compose config | grep -A 5 "volumes:"

# En VPS, verificar que volumen existe
docker volume ls | grep myapp

# Inspeccionar volumen
docker volume inspect myapp_db_data
```

**Prevención:**
- Asegurar que en `docker-compose.yml` está definido:
  ```yaml
  services:
    db:
      volumes:
        - db_data:/var/lib/postgresql/data
  volumes:
    db_data:
  ```

---

## Comandos Útiles para Operación

```bash
# Ver todos los deployments
docker ps -a

# Ver logs de un servicio (últimas 100 líneas)
docker logs --tail 100 -f <container_name>

# Entrar a un contenedor
docker exec -it <container_name> sh

# Reiniciar un servicio (sin perder datos)
docker-compose restart <service_name>

# Ver estado de volúmenes
docker volume ls

# Ver estado de redes
docker network ls

# Verificar uso de recursos
docker stats

# Redeploy forzado (sin tocar datos)
# En Coolify UI: click en deployment, "Redeploy"
```

---

## Resumen: Rol de Cada Actor

| Fase | Actor | Acción |
|------|-------|--------|
| Local | Desarrollador | Valida, prueba, commitea |
| GitHub | GitHub + Webhook | Recibe push, notifica a Coolify |
| Coolify | Coolify | Detecta cambio, construye, despliega |
| Monitoreo | DevOps / Arquitecto | Valida post-deploy, escala si es necesario |

---

## Integración con Otros Skills

- **Antes de push:** usa `coolify-deploy-checklist` ✅
- **Al validar compose:** usa `validate-coolify-compose` ✅
- **Si hay errores:** usa `validate-common-coolify-errors` para diagnosticar ✅
- **Para arquitectura general:** usa `coolify-architect-guidance` ✅

---

## Regla de Oro Final

> **"Configura Coolify una sola vez. Después, solo haz push a GitHub. Si el compose está correcto, Coolify hará el resto sin intervención."**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
