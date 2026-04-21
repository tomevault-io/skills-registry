---
name: architecture-skill
description: Mantiene la integridad arquitectónica del proyecto SmartKet siguiendo el patrón modular multi-tenant con separación de responsabilidades Use when this capability is needed.
metadata:
  author: aidora28
---

# Architecture Skill - SmartKet ERP

## 🎯 Propósito

Esta skill garantiza que todas las modificaciones al código mantengan la **integridad arquitectónica** del proyecto SmartKet, siguiendo los principios establecidos en el "Pentágono de Calidad" y la arquitectura modular multi-tenant.

## 🏗️ Principios Fundamentales

### 1. **Arquitectura de 3 Proyectos**
- **smartket-api** (Backend API) - Laravel en modo API
- **smartket-landing** (Marketing/Auth) - Laravel + Blade
- **smartket-app** (SPA Cliente) - Vue 3 + Vite

**Regla**: Nunca mezclar responsabilidades entre proyectos.

### 2. **Multi-Tenancy Database-per-Client**
- **Landlord DB** (`smartket_admin_db`): Gestión SaaS, usuarios, tenants
- **Tenant DBs** (`smartket_cliente_xyz`): Datos aislados por cliente

**Regla**: Modelos deben ser conscientes de en qué conexión operan.

### 3. **Separación de Responsabilidades Backend**

```
app/
├── Http/Controllers/Api/
│   ├── Core/           # Infraestructura base (Auth, Tenant, Branch, Setup)
│   ├── Admin/          # Administración (Staff, Roles)
│   ├── Compartido/     # Genérico para todos los negocios (Products, Sales)
│   └── [Vertical]/     # Específico del tipo de negocio (Polleria, Farmacia)
├── Models/
│   ├── Core/           # User, Tenant, Branch, Role, Permission, Module, Staff
│   ├── Compartido/     # Product, Sale, SaleItem, Category, CashRegister
│   └── [Vertical]/     # Order, OrderItem, Table (Polleria)
├── Services/
│   ├── Core/           # TenantService, AuthService, RolePermissionService
│   └── [Vertical]/     # PolleriaService
└── TenantFinders/      # HeaderTenantFinder
```

**Reglas PSR-4**:
- `App\Http\Controllers\Api\Core\*` → `app/Http/Controllers/Api/Core/*.php`
- `App\Models\Core\*` → `app/Models/Core/*.php`
- `App\Services\Core\*` → `app/Services/Core/*.php`

### 4. **Patrón de Capas**

```
Request → Route → Middleware → Controller → Service → Model → Database
```

**Responsabilidades**:
- **Controllers**: Validación de requests, routing
- **Services**: Lógica de negocio, orquestación
- **Models**: Acceso a datos, relaciones

**Anti-patrón**: Lógica de negocio en controllers ("Fat Controllers")

### 5. **Frontend Modular (Vue)**

```
src/
├── components/
│   ├── compartido/
│   │   ├── layout/     # TheSidebar, TheHeader
│   │   └── ui/         # SKButton, SKCard, Modal
│   └── [vertical]/     # Componentes específicos
├── views/
│   ├── compartido/     # Dashboard, Ventas, Productos
│   ├── core/           # Onboarding, Setup, SelectBranch
│   ├── admin/          # RolesPage, StaffPage
│   └── [vertical]/     # Vistas específicas (polleria/)
└── stores/             # Pinia stores
```

**Regla**: No duplicar componentes - si es compartido, va a `/compartido`.

---

## 📋 Checklist de Validación Arquitectónica

Antes de considerar una modificación completa, verificar:

### Backend
- [ ] ¿El controller solo valida y delega?
- [ ] ¿La lógica de negocio está en Services?
- [ ] ¿Los namespaces son PSR-4 compliant?
- [ ] ¿El modelo está en la carpeta correcta (Core/Compartido/Vertical)?
- [ ] ¿Se respeta la conexión DB (landlord vs tenant)?
- [ ] ¿Hay duplicación de código que deba ir a Compartido?

### Frontend
- [ ] ¿Los componentes reutilizables están en `/compartido`?
- [ ] ¿Las vistas están en la carpeta correcta (compartido/core/vertical)?
- [ ] ¿Se usa composables para lógica reutilizable?
- [ ] ¿No hay llamadas directas a API sin axios/fetch wrapper?

### Multi-Tenant
- [ ] ¿El modelo usa `UsesTenantConnection` trait si aplica?
- [ ] ¿Los seeders/migrations saben si son landlord o tenant?
- [ ] ¿Las rutas API verifican tenant si es necesario?

---

## 🛠️ Scripts de Validación

### 1. Validar Estructura de Carpetas
```bash
# Desde raíz del proyecto
.\.agent\skills\architecture\scripts\validate-structure.ps1
```

**Qué hace**: Verifica que no haya archivos en ubicaciones incorrectas.

### 2. Verificar Namespaces PSR-4
```bash
cd smartket-api
php ..\.agent\skills\architecture\scripts\check-namespaces.php
```

**Qué hace**: Valida que los namespaces coincidan con las rutas físicas.

### 3. Analizar Acoplamiento
```bash
cd smartket-api
php ..\.agent\skills\architecture\scripts\analyze-coupling.php
```

**Qué hace**: Detecta controllers con lógica de negocio (violación de patrón).

---

## 📚 Ejemplos de Patrones Correctos

### Controller Pattern
Ver: `.agent/skills/architecture/examples/controller-pattern.php`

```php
// ✅ CORRECTO - Controller delgado
public function store(Request $request)
{
    $validated = $request->validate([...]);
    $result = $this->service->createResource($validated);
    return response()->json($result, 201);
}
```

### Service Pattern
Ver: `.agent/skills/architecture/examples/service-pattern.php`

```php
// ✅ CORRECTO - Service con lógica de negocio
public function createResource(array $data): Model
{
    DB::transaction(function() use ($data) {
        $resource = Resource::create($data);
        $this->notifyStakeholders($resource);
        return $resource;
    });
}
```

### Repository Pattern (Opcional)
Ver: `.agent/skills/architecture/examples/repository-pattern.php`

Para queries complejas, considerar Repository intermediario.

---

## 🚨 Anti-Patrones Comunes

Ver: `.agent/skills/architecture/resources/anti-patterns.md`

1. **Fat Controllers** - Lógica de negocio en controller
2. **God Classes** - Services que hacen todo
3. **Namespace Pollution** - Imports innecesarios
4. **Tight Coupling** - Modelos con dependencias directas a services
5. **Feature Envy** - Controllers accediendo directamente a modelos de otros módulos

---

## 🔄 Workflow de Modificación

### Cuando agregas funcionalidad:

1. **Identificar módulo**: ¿Core, Compartido, o Vertical?
2. **Crear Service** (si no existe): Lógica de negocio aquí
3. **Crear Controller**: Solo validación y llamada a service
4. **Crear/Actualizar Model**: Relaciones y scopes
5. **Actualizar Routes**: Con namespace correcto
6. **Ejecutar validaciones**: Scripts de esta skill
7. **Actualizar documentación**: Si cambia arquitectura

### Cuando refactorizas:

1. **Identificar violación**: ¿Qué principio se viola?
2. **Planificar movimiento**: ¿A dónde debe ir el código?
3. **Actualizar namespaces**: PSR-4 compliance
4. **Ejecutar tests**: Antes y después
5. **Validar con scripts**: De esta skill

---

## 📖 Referencias

- [Documento Maestro de Arquitectura](file:///d:/TRAE/smartket-v1/DOCUMENTO/Documento%20Maestro%20de%20Arquitectura%20-%20SmartKet%20ERP.md)
- [Guía de Buenas Prácticas](file:///d:/TRAE/smartket-v1/DOCUMENTO/Guía%20de%20Buenas%20Prácticas%20y%20Filosofía%20de%20Desarrollo%20para%20SmartKet.md)
- [RECYCLING_PLAN.md](file:///d:/TRAE/smartket-v1/RECYCLING_PLAN.md)

---

## 🎓 Cuándo Usar Esta Skill

✅ **Usar cuando**:
- Agregas nuevos controllers/services/models
- Refactorizas código existente
- Reorganizas estructura de carpetas
- Actualizas namespaces
- Integras módulo de nuevo vertical (ej. Farmacia)

❌ **No usar para**:
- Bugs simples que no afectan arquitectura
- Cambios de UI/CSS
- Actualizaciones de dependencias
- Configuración de environment

---

## 💡 Filosofía

> "La arquitectura no es lo que construyes, es lo que te permite construir después" 

Esta skill no limita creatividad - establece **carriles de alta velocidad** para que desarrollo futuro sea rápido, seguro y predecible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidora28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
