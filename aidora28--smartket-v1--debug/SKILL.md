---
name: debug-skill
description: Diagnostica errores comunes en producción rápidamente Use when this capability is needed.
metadata:
  author: aidora28
---

# Debug Skill - SmartKet ERP

## 🎯 Propósito

Esta skill facilita el **diagnóstico rápido de errores** en producción y desarrollo, con scripts automatizados y catálogo de soluciones para problemas comunes.

## 🔍 Categorías de Errores

### 1. CORS Issues (500 Internal Error con navegador)
**Síntomas**: Request funciona en Postman, falla en navegador

**Diagnóstico**:
```bash
.\Pruebas\test-cors.ps1
```

**Checklist**:
- [ ] Middleware Cors en `bootstrap/app.php`
- [ ] Variable `CORS_ALLOWED_ORIGINS` en `.env`
- [ ] Servidor reiniciado después de cambios

### 2. Namespace Errors (Class not found)
**Síntomas**: `Class "App\Models\XYZ" not found`

**Diagnóstico**:
```bash
cd smartket-api
php ..\.agent\skills\architecture\scripts\check-namespaces.php
composer dump-autoload
```

**Checklist**:
- [ ] Namespace coincide con ubicación física
- [ ] PSR-4 compliant
- [ ] Composer autoload actualizado

### 3. Database Connection Failures
**Síntomas**: `Connection refused`, `database does not exist`

**Diagnóstico**:
```bash
cd smartket-api
php ..\.agent\skills\debug\scripts\check-connections.php
```

**Checklist**:
- [ ] PostgreSQL corriendo
- [ ] Credenciales en `.env` correctas
- [ ] DB existe (`psql -l` para verificar)

### 4. Token Expiration/Invalid
**Síntomas**: `401 Unauthorized`, `Token expired`

**Diagnóstico**:
```bash
cd smartket-api
php ..\.agent\skills\debug\scripts\trace-auth.php --email=user@example.com
```

**Checklist**:
- [ ] Token no expirado (verificar `expires_at`)
- [ ] Header `Authorization: Bearer` correcto
- [ ] Cookie HTTP-only presente

### 5. Multi-Tenant Isolation Breach
**Síntomas**: Usuario ve datos de otro tenant

**Diagnóstico**:
```bash
cd smartket-api
php ..\.agent\skills\debug\scripts\audit-tenant-isolation.php
```

**Checklist**:
- [ ] Header `X-Tenant-ID` presente
- [ ] Modelos usan conexión `tenant`
- [ ] Global scopes aplicados

---

## 🛠️ Scripts de Diagnóstico

### 1. Analizar Logs
```powershell
.\. agent\skills\debug\scripts\analyze-logs.ps1 -Last 100 -Filter "error"
```

**Output**: Errores más frecuentes y stack traces

### 2. Verificar Conexiones
```bash
php ..\.agent\skills\debug\scripts\check-connections.php
```

**Output**: Estado de DB, cache, Redis (si aplica)

### 3. Trace Request Específico
```bash
php ..\.agent\skills\debug\scripts\trace-request.php --endpoint="/api/products" --method=POST
```

**Output**: Middlewares ejecutados, queries, timing

### 4. Performance Profiler
```bash
php ..\.agent\skills\debug\scripts\performance-profiler.php
```

**Output**: Endpoints lentos, N+1 queries, memory usage

---

## 📋 Catálogo de Errores Comunes

Ver: `.agent/skills/debug/resources/common-errors.md`

**Incluye**:
- Error 500: Causas y soluciones
- Error 419: CSRF token mismatch
- Error 403: Forbidden (RBAC)
- Error 422: Validation errors
- Errores de migraciones
- Errores de seeding

---

## 🔄 Workflow de Debugging

### Paso 1: Reproducir
1. Obtener pasos exactos del error
2. Verificar en ambiente local
3. Revisar logs: `tail -f storage/logs/laravel.log`

### Paso 2: Diagnosticar
1. Ejecutar script relevante de esta skill
2. Revisar stack trace completo
3. Identificar componente problemático

### Paso 3: Corregir
1. Aplicar fix
2. Ejecutar tests relacionados
3. Verificar en ambientes

### Paso 4: Prevenir
1. Agregar test que cubra el caso
2. Actualizar docs si es patrón común
3. Registrar en CHANGELOG

---

## 🚨 Decision Tree de Debugging

```
Error Ocurrió
├─ ¿En qué capa?
│  ├─ Frontend → Ver console.log, network tab
│  ├─ Backend → Ver laravel.log
│  └─ Base de Datos → Ver PostgreSQL logs
│
├─ ¿Es reproducible?
│  ├─ Sí → Ejecutar script diagnóstico
│  └─ No → Verificar race conditions, cache
│
└─ ¿Afecta a todos los tenants?
   ├─ Sí → Problema en landlord o Core
   └─ No → Problema en tenant específico
```

---

## 💡 Tips de Debugging

### 1. Usar `dd()` estratégicamente
```php
// ❌ Malo
dd($variable); // Termina ejecución temprano

// ✅ Bueno
Log::debug('Variable value', ['var' => $variable]); // Continúa
```

### 2. Query Debugging
```php
// Ver todas las queries ejecutadas
DB::enableQueryLog();
// ... código ...
dd(DB::getQueryLog());
```

### 3. Request Debugging
```php
// En controller, ver todo el request
Log::debug('Full request', [
    'headers' => $request->headers->all(),
    'body' => $request->all(),
    'user' => auth()->user()
]);
```

---

## 🎓 Cuándo Usar Esta Skill

✅ **Usar cuando**:
- Error en producción que no se reproduce localmente
- Performance degradado sin causa obvia
- Bug reportado por usuario
- Comportamiento extraño multi-tenant

❌ **No reemplaza**:
- Debugger IDE (XDebug)
- Profilers avanzados (Telescope, Clockwork)
- Monitoring (Sentry, New Relic)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidora28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
