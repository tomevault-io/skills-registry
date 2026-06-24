---
name: ui-patterns
description: Patrones de interfaz de usuario comunes y mejores prácticas Use when this capability is needed.
metadata:
  author: kaltwulx
---

# UI Patterns

Catálogo de patrones de interfaz probados y usabilidad garantizada.

## Patrones de Navegación

### Tabs
**Cuándo usar:**
- Contenido organizado en secciones mutuamente excluyentes
- 3-7 categorías máximo
- Espacio horizontal disponible

**Mejores prácticas:**
- Primera tab activa por defecto
- Nombres cortos y descriptivos
- Indicador visual de tab activa
- Responsive: convertir a dropdown en mobile

```
[ Tab 1 ] [ Tab 2 ] [ Tab 3 ]
┌─────────────────────────────┐
│ Contenido activo            │
└─────────────────────────────┘
```

### Breadcrumbs
**Cuándo usar:**
- Jerarquía de navegación profunda (>2 niveles)
- Necesidad de volver atrás fácilmente
- E-commerce, documentación, dashboards

**Estructura:**
```
Home > Category > Subcategory > Current Page
```

### Sidebar Navigation
**Cuándo usar:**
- Muchas secciones (>5)
- Navegación persistente necesaria
- Aplicaciones complejas

**Variantes:**
- **Fixed**: Siempre visible
- **Collapsible**: Se oculta/muestra
- **Icon + Text**: Iconos con etiquetas
- **Icon Only**: Solo iconos (con tooltips)

## Patrones de Input

### Smart Defaults
Reducir carga cognitiva con valores predefinidos inteligentes:
- País basado en IP
- Fecha actual para fechas recientes
- Opciones más comunes primero

### Progressive Disclosure
Mostrar solo lo necesario:
- Campos opcionales ocultos tras "Avanzado"
- Formularios multi-step
- Validación inline

### Input Validation
**Timing:**
- ❌ Validar onBlur (frustrante)
- ✅ Validar onSubmit o después de delay
- ✅ Validación positiva inmediata (green check)

**Mensajes:**
- Claros y accionables
- "Email inválido" → "Ingresa un email válido (ej: usuario@dominio.com)"

## Patrones de Feedback

### Loading States
**Opciones:**
1. **Skeleton Screens**: Para contenido que tarda >200ms
2. **Spinners**: Para acciones que tardan <1s
3. **Progress Bars**: Para operaciones largas (>3s)

### Empty States
Nunca dejar pantallas vacías:
- Ilustración amigable
- Explicación del estado
- CTA claro ("Crear primer elemento")

### Toast Notifications
**Cuándo usar:**
- Feedback de acciones completadas
- No bloqueantes
- Auto-dismiss después de 3-5s

**Niveles:**
- ✅ Success: Verde
- ℹ️ Info: Azul
- ⚠️ Warning: Amarillo
- ❌ Error: Rojo

## Patrones de Búsqueda

### Search with Filters
```
┌────────────────────────────────────┐
│ 🔍 Buscar...     [Filtros ▼]       │
├────────────────────────────────────┤
│ 10 resultados                      │
│ [Resultado 1]                      │
│ [Resultado 2]                      │
└────────────────────────────────────┘
```

**Mejores prácticas:**
- Autocomplete con suggestions
- Búsqueda fuzzy (tolerante a typos)
- Highlight de términos encontrados
- Filtros persistentes en URL

### Faceted Search
Para catálogos grandes:
- Filtros acumulativos (AND logic)
- Contadores en opciones
- Clear all filters
- Mobile: filters drawer

## Responsive Patterns

### Mobile-First Approach
1. Diseñar para mobile primero
2. Escalar a tablet/desktop
3. Priorizar contenido esencial

### Breakpoints Comunes
```css
/* Mobile first */
default: 0-639px
sm: 640px
md: 768px
lg: 1024px
xl: 1280px
2xl: 1536px
```

### Touch Targets
- Mínimo 44x44px (iOS)
- Mínimo 48x48px (Android)
- Espacio entre targets: 8px mínimo

## Accesibilidad (A11y)

### Contraste
- Texto normal: 4.5:1 mínimo
- Texto grande: 3:1 mínimo
- UI components: 3:1 mínimo

### Focus Indicators
- Siempre visibles
- Alto contraste
- No depender solo de color

### Screen Readers
- Labels descriptivos
- Alt text en imágenes
- ARIA labels cuando necesario
- Skip links para navegación

## Anti-Patrones

❌ **Evitar:**
- Dropdowns con >15 opciones (usar search)
- Placeholders como labels
- Modales sobre modales
- Infinite scroll sin footer
- Scroll hijacking
- Autoplay de video/audio
- Links que no parecen links

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaltwulx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
