---
name: business-requirements-analyst
description: Metodología completa para levantamiento de requerimientos de software y negocios. Usar cuando se necesite documentar un proyecto nuevo, validar una idea de negocio, crear especificaciones técnicas, o generar documentación completa que cubra visión de negocio, stakeholders, procesos, requerimientos funcionales y no funcionales, modelo de datos, integraciones, riesgos y roadmap. Ideal para proyectos que requieren cotización, desarrollo, delegación o presentación a inversores. Use when this capability is needed.
metadata:
  author: neversight
---

# Requirements Gathering - Levantamiento de Requerimientos

Este Skill proporciona una metodología profesional y completa para realizar levantamiento de requerimientos que cubra todas las capas del negocio, no solo "la app" o "el sistema".

## Principio Fundamental

**El levantamiento de requerimientos NO es solo una lista de funcionalidades.**

Un buen levantamiento cubre todas las capas del negocio:

- 🧠 Idea y objetivo
- 👥 Usuarios
- 💼 Operación
- 💰 Dinero
- ⚙️ Tecnología
- ⚠️ Riesgos
- 📈 Crecimiento

## Metodologías Disponibles

### 1. Design Thinking (para ideas nuevas)

**Ideal cuando:**

- La idea aún se está validando
- No tienes todo claro
- Quieres entender al usuario de verdad

**Fases:**

1. **Empatizar** - Entender a los usuarios
2. **Definir** - Identificar problemas clave
3. **Idear** - Generar soluciones
4. **Prototipar** - Crear versiones tempranas
5. **Testear** - Validar con usuarios reales

**Nota:** Sirve para descubrir qué construir, no para documentarlo todo.

### 2. Análisis de Negocio (Business Analysis - BABOK)

**El más completo y profesional.**

**Cubre:**

- Requerimientos del negocio
- Requerimientos funcionales
- Requerimientos no funcionales
- Reglas del negocio
- Stakeholders
- Procesos

**Nota:** Este es el que se usa para crear documentos formales.

### 3. Lean / Startup Canvas (visión rápida)

**Sirve para:**

- Ordenar la idea
- Ver si el negocio tiene sentido

**⚠️ Importante:** NO reemplaza el levantamiento de requerimientos completo.

## Enfoque Recomendado

Combinar 3 elementos clave:

1. **Visión de negocio**
2. **Procesos**
3. **Sistema / producto**

Y documentarlo todo en un **artefacto maestro único**.

## Estructura del Documento Completo de Requerimientos

### 📌 1. Visión del Negocio

Incluir:

- **Problema que resuelve** - ¿Qué dolor o necesidad atiende?
- **Propuesta de valor** - ¿Por qué es mejor que las alternativas?
- **Objetivo del negocio** - Metas claras y medibles
- **KPIs principales** - Métricas de éxito
- **Alcance** - Qué SÍ y qué NO está incluido

**Ejemplo de formato:**

```markdown
## Visión del Negocio

### Problema

[Descripción del problema que se resuelve]

### Propuesta de Valor

[Qué hace único a este producto/servicio]

### Objetivos

- Objetivo 1: [Descripción]
- Objetivo 2: [Descripción]

### KPIs

- KPI 1: [Métrica específica]
- KPI 2: [Métrica específica]

### Alcance

**Incluye:**

- [Elemento 1]
- [Elemento 2]

**No incluye:**

- [Elemento 1]
- [Elemento 2]
```

### 📌 2. Stakeholders

Identificar todos los actores involucrados:

- **Dueños del negocio** - Quiénes toman decisiones
- **Usuarios finales** - Quiénes usarán el sistema
- **Administradores** - Quiénes gestionarán el sistema
- **Proveedores** - Servicios o productos externos
- **Terceros** - Pagos, logística, integraciones, etc.

**Formato sugerido:**

```markdown
## Stakeholders

| Tipo    | Nombre/Rol | Interés           | Influencia        |
| ------- | ---------- | ----------------- | ----------------- |
| Dueño   | [Nombre]   | [Alto/Medio/Bajo] | [Alta/Media/Baja] |
| Usuario | [Tipo]     | [Alto/Medio/Bajo] | [Alta/Media/Baja] |
```

### 📌 3. Tipos de Usuarios (Personas)

Para cada tipo de usuario, documentar:

- **Qué necesita** - Funcionalidades clave
- **Qué dolor tiene** - Problemas actuales
- **Qué espera del sistema** - Expectativas

**Ejemplo:**

```markdown
## Personas

### Cliente Final

- **Necesita:** Realizar compras rápidas y seguras
- **Dolor:** Procesos de pago complicados
- **Espera:** Checkout en menos de 3 clics

### Administrador

- **Necesita:** Gestionar inventario y pedidos
- **Dolor:** Falta de visibilidad en tiempo real
- **Espera:** Dashboard con métricas actualizadas
```

### 📌 4. Procesos del Negocio

**⚠️ MUY IMPORTANTE - Aquí muchos fallan.**

Documentar flujos completos:

- Cómo entra un cliente
- Cómo se genera una venta
- Cómo se cobra
- Qué pasa si falla el pago
- Cómo se atiende un reclamo

**Expresar como flujos paso a paso:**

```markdown
## Proceso: Compra de Producto

1. Usuario navega catálogo
2. Usuario agrega productos al carrito
3. Usuario procede al checkout
4. Sistema valida disponibilidad
5. Usuario ingresa datos de pago
6. Sistema procesa pago
   - **Si éxito:** Confirma pedido y envía email
   - **Si falla:** Muestra error y permite reintentar
7. Sistema genera orden de envío
8. Usuario recibe confirmación
```

Para procesos complejos, ver [references/process-mapping.md](references/process-mapping.md).

### 📌 5. Requerimientos Funcionales

Formato estándar:

- **RF-01:** El sistema debe permitir...
- **RF-02:** El usuario podrá...

**Categorías comunes:**

- Registro de usuarios
- Gestión de pedidos
- Pagos
- Notificaciones
- Reportes

**Ejemplo:**

```markdown
## Requerimientos Funcionales

### Autenticación

- **RF-01:** El sistema debe permitir registro con email y contraseña
- **RF-02:** El sistema debe enviar email de verificación
- **RF-03:** El usuario podrá recuperar contraseña olvidada

### Gestión de Pedidos

- **RF-04:** El usuario podrá ver historial de pedidos
- **RF-05:** El sistema debe permitir cancelar pedidos en estado "pendiente"
```

### 📌 6. Requerimientos No Funcionales

**Esto separa lo amateur de lo profesional.**

Áreas clave:

- **Seguridad** - Autenticación, autorización, encriptación
- **Rendimiento** - Tiempos de respuesta, capacidad
- **Escalabilidad** - Crecimiento esperado
- **Disponibilidad** - Uptime, redundancia
- **Cumplimiento legal** - GDPR, protección de datos
- **UX / Usabilidad** - Accesibilidad, responsive

**Ejemplo:**

```markdown
## Requerimientos No Funcionales

### Rendimiento

- **RNF-01:** El sistema debe responder en < 2 segundos para el 95% de las peticiones
- **RNF-02:** El sistema debe soportar 1000 usuarios concurrentes

### Seguridad

- **RNF-03:** Todas las contraseñas deben estar hasheadas con bcrypt
- **RNF-04:** Las comunicaciones deben usar HTTPS/TLS 1.3

### Cumplimiento

- **RNF-05:** El sistema debe cumplir con GDPR para datos de usuarios europeos
```

### 📌 7. Reglas del Negocio

Lógica específica del dominio:

**Ejemplos:**

```markdown
## Reglas del Negocio

- **RN-01:** Un pedido no puede cancelarse después de 30 minutos de creado
- **RN-02:** Un usuario solo puede tener un plan activo a la vez
- **RN-03:** Las comisiones se calculan como 5% del monto total
- **RN-04:** Los impuestos se aplican según la región del comprador
```

### 📌 8. Modelo de Datos (Alto Nivel)

**Conceptual, no SQL aún.**

Documentar:

- **Entidades principales** - Usuario, Pedido, Producto, etc.
- **Relaciones** - Uno a muchos, muchos a muchos
- **Datos críticos** - Campos esenciales

**Ejemplo:**

```markdown
## Modelo de Datos

### Entidades Principales

**Usuario**

- id (PK)
- email (único)
- nombre
- fecha_registro

**Pedido**

- id (PK)
- usuario_id (FK)
- estado
- total
- fecha_creacion

**Producto**

- id (PK)
- nombre
- precio
- stock

### Relaciones

- Un Usuario puede tener muchos Pedidos (1:N)
- Un Pedido puede contener muchos Productos (N:M)
```

Para modelos complejos, ver [references/data-modeling.md](references/data-modeling.md).

### 📌 9. Integraciones

Servicios externos necesarios:

- **Pasarelas de pago** - Stripe, PayPal, etc.
- **APIs externas** - Servicios de terceros
- **Servicios de terceros** - Email, SMS, analytics

**Ejemplo:**

```markdown
## Integraciones

### Pasarela de Pago

- **Proveedor:** Stripe
- **Funcionalidad:** Procesamiento de tarjetas de crédito
- **Datos intercambiados:** Monto, moneda, token de tarjeta

### Servicio de Email

- **Proveedor:** SendGrid
- **Funcionalidad:** Envío de notificaciones
- **Datos intercambiados:** Destinatario, asunto, cuerpo HTML
```

### 📌 10. Riesgos y Supuestos

Identificar potenciales problemas:

- **Riesgos técnicos** - Dependencias, escalabilidad
- **Riesgos legales** - Cumplimiento, privacidad
- **Suposiciones del negocio** - Asunciones que deben validarse

**Ejemplo:**

```markdown
## Riesgos

### Técnicos

- **R-01:** Dependencia de API externa puede causar downtime
  - _Mitigación:_ Implementar sistema de caché y fallback

### Legales

- **R-02:** Cambios en regulación de protección de datos
  - _Mitigación:_ Diseño modular para adaptación rápida

## Supuestos

- **S-01:** Los usuarios tienen acceso a internet estable
- **S-02:** El volumen inicial no excederá 10,000 usuarios
```

### 📌 11. Roadmap / Fases

Dividir en etapas manejables:

**Ejemplo:**

```markdown
## Roadmap

### MVP (Fase 1) - 3 meses

- Registro y autenticación
- Catálogo de productos
- Carrito de compras
- Pago básico con Stripe

### Fase 2 - 2 meses

- Sistema de notificaciones
- Historial de pedidos
- Panel de administración básico

### Fase 3 - 3 meses

- Reportes avanzados
- Integración con logística
- Sistema de recomendaciones
```

## Proceso de Levantamiento (Paso a Paso)

### 1. Entrevistas

**Aunque seas tú mismo el stakeholder**, realiza el ejercicio de responder:

- ¿Qué problema resuelve esto?
- ¿Quiénes lo usarán?
- ¿Cómo lo usarán?
- ¿Qué alternativas existen?
- ¿Por qué esto es mejor?

### 2. Preguntas Incómodas

**Fundamental para descubrir edge cases:**

- ¿Qué pasa si falla el pago?
- ¿Qué pasa si el usuario pierde conexión?
- ¿Qué pasa si hay datos duplicados?
- ¿Qué pasa si el servicio externo está caído?

### 3. Diagramar Flujos

Crear diagramas visuales de:

- Flujos de usuario (user flows)
- Procesos de negocio (business processes)
- Arquitectura del sistema (system architecture)

### 4. Escribir → Validar → Ajustar

**Proceso iterativo:**

1. Escribir primera versión del documento
2. Revisar con stakeholders
3. Identificar gaps y ambigüedades
4. Ajustar y refinar
5. Repetir hasta tener consenso

### 5. Documento Vivo

**Mantener actualizado:**

- Usar formato Markdown para versionado
- Herramientas: Notion, Confluence, GitHub Wiki
- Actualizar cuando cambien requerimientos

## Plantilla Completa

Para una plantilla lista para usar, ver [assets/requirements-template.md](assets/requirements-template.md).

## Resultado Final

Cuando terminas el levantamiento completo, tienes:

- ✅ **Documento para desarrollar** - Especificaciones claras
- ✅ **Base para cotizar** - Alcance definido
- ✅ **Guía para delegar** - Instrucciones completas
- ✅ **Material para presentar** - A socios o inversionistas

## Consejos Importantes

1. **No empieces escribiendo requerimientos** - Empieza entendiendo el negocio como si ya existiera
2. **Sé específico** - "Rápido" no es un requerimiento, "< 2 segundos" sí lo es
3. **Incluye el "por qué"** - No solo el "qué", también la razón detrás
4. **Documenta decisiones** - Por qué se eligió X tecnología o enfoque
5. **Mantén actualizado** - Un documento desactualizado es peor que no tener documento

## Referencias Adicionales

Para técnicas avanzadas y ejemplos específicos:

- **Mapeo de procesos complejos:** [references/process-mapping.md](references/process-mapping.md)
- **Modelado de datos avanzado:** [references/data-modeling.md](references/data-modeling.md)
- **Casos de uso detallados:** [references/use-cases.md](references/use-cases.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
