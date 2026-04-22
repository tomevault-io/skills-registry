---
name: rapid-tailwind-components
description: Proporciona componentes Tailwind CSS pre-construidos, optimizados y listos para copiar-pegar, con variantes profesionales (botones, formularios, tarjetas, navegación, etc.) para desarrollo UI rápido sin tokens. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Componentes Tailwind CSS Rápidos

## Propósito

Ofrecer **biblioteca de componentes Tailwind CSS** pre-construidos y listos para usar, minimizando tokens al copiar/pegar directamente en tu proyecto. Cada componente incluye variantes profesionales y accesibles.

---

## 1. BOTONES

### Botón Principal (CTA)
```html
<button class="px-6 py-3 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
  Acción Principal
</button>
```

### Botón Secundario
```html
<button class="px-6 py-3 bg-gray-200 text-gray-900 font-semibold rounded-lg hover:bg-gray-300 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2">
  Acción Secundaria
</button>
```

### Botón Outline
```html
<button class="px-6 py-3 border-2 border-blue-600 text-blue-600 font-semibold rounded-lg hover:bg-blue-50 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
  Acción Outline
</button>
```

### Botón Peligroso
```html
<button class="px-6 py-3 bg-red-600 text-white font-semibold rounded-lg hover:bg-red-700 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
  Eliminar
</button>
```

### Botón Deshabilitado
```html
<button class="px-6 py-3 bg-gray-400 text-white font-semibold rounded-lg cursor-not-allowed opacity-60" disabled>
  Deshabilitado
</button>
```

### Botón Pequeño (Compact)
```html
<button class="px-3 py-1.5 bg-blue-600 text-white text-sm font-medium rounded hover:bg-blue-700 transition-colors">
  Pequeño
</button>
```

### Botón Grande (Hero)
```html
<button class="px-8 py-4 bg-blue-600 text-white text-lg font-bold rounded-xl hover:bg-blue-700 transition-colors duration-200 shadow-lg hover:shadow-xl">
  Acción Destacada
</button>
```

---

## 2. FORMULARIOS

### Input de Texto Básico
```html
<div class="mb-4">
  <label class="block text-sm font-semibold text-gray-700 mb-2">
    Correo Electrónico
  </label>
  <input 
    type="email" 
    class="w-full px-4 py-2.5 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all"
    placeholder="tu@email.com"
  />
</div>
```

### Input con Error
```html
<div class="mb-4">
  <label class="block text-sm font-semibold text-gray-700 mb-2">
    Contraseña
  </label>
  <input 
    type="password" 
    class="w-full px-4 py-2.5 border-2 border-red-500 rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 focus:border-transparent"
  />
  <p class="mt-2 text-sm text-red-600">Contraseña muy corta</p>
</div>
```

### Input con Éxito
```html
<div class="mb-4">
  <label class="block text-sm font-semibold text-gray-700 mb-2">
    Usuario
  </label>
  <input 
    type="text" 
    class="w-full px-4 py-2.5 border-2 border-green-500 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent"
    value="usuario123"
  />
  <p class="mt-2 text-sm text-green-600">✓ Disponible</p>
</div>
```

### Select Dropdown
```html
<div class="mb-4">
  <label class="block text-sm font-semibold text-gray-700 mb-2">
    País
  </label>
  <select class="w-full px-4 py-2.5 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent">
    <option>Seleccionar...</option>
    <option>México</option>
    <option>Colombia</option>
    <option>Argentina</option>
  </select>
</div>
```

### Textarea
```html
<div class="mb-4">
  <label class="block text-sm font-semibold text-gray-700 mb-2">
    Mensaje
  </label>
  <textarea 
    class="w-full px-4 py-2.5 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent resize-none"
    rows="4"
    placeholder="Escribe aquí..."
  ></textarea>
</div>
```

### Checkbox
```html
<div class="flex items-center mb-4">
  <input 
    type="checkbox" 
    id="terms"
    class="w-4 h-4 text-blue-600 border-gray-300 rounded focus:ring-2 focus:ring-blue-500 cursor-pointer"
  />
  <label for="terms" class="ml-2 text-sm text-gray-700 cursor-pointer">
    Acepto los términos y condiciones
  </label>
</div>
```

### Radio Button
```html
<div class="mb-4">
  <p class="text-sm font-semibold text-gray-700 mb-2">Plan</p>
  <div class="space-y-2">
    <label class="flex items-center cursor-pointer">
      <input type="radio" name="plan" class="w-4 h-4 text-blue-600" />
      <span class="ml-2 text-sm text-gray-700">Plan Gratuito</span>
    </label>
    <label class="flex items-center cursor-pointer">
      <input type="radio" name="plan" class="w-4 h-4 text-blue-600" checked />
      <span class="ml-2 text-sm text-gray-700">Plan Premium</span>
    </label>
  </div>
</div>
```

---

## 3. TARJETAS (Cards)

### Tarjeta Simple
```html
<div class="bg-white rounded-lg border border-gray-200 p-6 hover:shadow-lg transition-shadow">
  <h3 class="text-lg font-bold text-gray-900 mb-2">Título</h3>
  <p class="text-gray-600 text-sm">Descripción breve del contenido de la tarjeta.</p>
</div>
```

### Tarjeta con Imagen
```html
<div class="bg-white rounded-lg overflow-hidden border border-gray-200 hover:shadow-lg transition-shadow">
  <div class="h-48 bg-gray-300"></div>
  <div class="p-6">
    <h3 class="text-lg font-bold text-gray-900 mb-2">Título</h3>
    <p class="text-gray-600 text-sm mb-4">Descripción breve.</p>
    <a href="#" class="text-blue-600 font-semibold text-sm hover:text-blue-700">Leer más →</a>
  </div>
</div>
```

### Tarjeta de Pricing
```html
<div class="border border-gray-200 rounded-lg p-8 text-center hover:border-blue-500 transition-colors">
  <h3 class="text-xl font-bold text-gray-900 mb-2">Plan Pro</h3>
  <p class="text-4xl font-bold text-gray-900 mb-4">$29<span class="text-lg text-gray-600">/mes</span></p>
  <ul class="text-sm text-gray-600 mb-6 space-y-2">
    <li>✓ 100 usuarios</li>
    <li>✓ Soporte 24/7</li>
    <li>✓ API ilimitada</li>
  </ul>
  <button class="w-full px-6 py-3 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700 transition-colors">
    Comenzar Ahora
  </button>
</div>
```

### Tarjeta de Feature
```html
<div class="bg-white p-6 rounded-lg border border-gray-200">
  <div class="w-12 h-12 bg-blue-100 rounded-lg flex items-center justify-center mb-4">
    <svg class="w-6 h-6 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
      <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"/>
    </svg>
  </div>
  <h3 class="text-lg font-bold text-gray-900 mb-2">Característica</h3>
  <p class="text-gray-600 text-sm">Descripción detallada de esta característica.</p>
</div>
```

---

## 4. NAVEGACIÓN

### Navbar Horizontal
```html
<nav class="bg-white border-b border-gray-200">
  <div class="max-w-7xl mx-auto px-6 py-4 flex justify-between items-center">
    <div class="text-xl font-bold text-gray-900">Logo</div>
    <div class="flex gap-6">
      <a href="#" class="text-gray-700 hover:text-blue-600 font-medium text-sm">Inicio</a>
      <a href="#" class="text-gray-700 hover:text-blue-600 font-medium text-sm">Productos</a>
      <a href="#" class="text-gray-700 hover:text-blue-600 font-medium text-sm">Contacto</a>
    </div>
    <button class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">Login</button>
  </div>
</nav>
```

### Breadcrumb
```html
<nav class="flex items-center gap-2 text-sm mb-6">
  <a href="#" class="text-blue-600 hover:text-blue-700">Inicio</a>
  <span class="text-gray-400">/</span>
  <a href="#" class="text-blue-600 hover:text-blue-700">Productos</a>
  <span class="text-gray-400">/</span>
  <span class="text-gray-700 font-semibold">Detalle</span>
</nav>
```

### Tabs (Pestañas)
```html
<div class="border-b border-gray-200 mb-6">
  <div class="flex gap-8">
    <button class="pb-4 font-semibold text-blue-600 border-b-2 border-blue-600">
      Tab Activa
    </button>
    <button class="pb-4 font-semibold text-gray-600 hover:text-gray-900 border-b-2 border-transparent">
      Tab Inactiva
    </button>
    <button class="pb-4 font-semibold text-gray-600 hover:text-gray-900 border-b-2 border-transparent">
      Otra Tab
    </button>
  </div>
</div>
```

### Pagination
```html
<div class="flex items-center justify-center gap-2">
  <button class="px-3 py-2 border border-gray-300 rounded hover:bg-gray-100">← Anterior</button>
  <button class="px-3 py-2 bg-blue-600 text-white rounded">1</button>
  <button class="px-3 py-2 border border-gray-300 rounded hover:bg-gray-100">2</button>
  <button class="px-3 py-2 border border-gray-300 rounded hover:bg-gray-100">3</button>
  <button class="px-3 py-2 border border-gray-300 rounded hover:bg-gray-100">Siguiente →</button>
</div>
```

---

## 5. ALERTAS Y MENSAJES

### Alert Success
```html
<div class="bg-green-50 border border-green-200 rounded-lg p-4 mb-4">
  <div class="flex items-start gap-3">
    <div class="flex-shrink-0 text-green-600 mt-0.5">✓</div>
    <div>
      <h3 class="font-semibold text-green-900">¡Éxito!</h3>
      <p class="text-green-800 text-sm mt-1">Tu operación se completó correctamente.</p>
    </div>
  </div>
</div>
```

### Alert Error
```html
<div class="bg-red-50 border border-red-200 rounded-lg p-4 mb-4">
  <div class="flex items-start gap-3">
    <div class="flex-shrink-0 text-red-600 font-bold mt-0.5">!</div>
    <div>
      <h3 class="font-semibold text-red-900">Error</h3>
      <p class="text-red-800 text-sm mt-1">Algo salió mal. Por favor intenta de nuevo.</p>
    </div>
  </div>
</div>
```

### Alert Warning
```html
<div class="bg-yellow-50 border border-yellow-200 rounded-lg p-4 mb-4">
  <div class="flex items-start gap-3">
    <div class="flex-shrink-0 text-yellow-600 font-bold mt-0.5">⚠</div>
    <div>
      <h3 class="font-semibold text-yellow-900">Advertencia</h3>
      <p class="text-yellow-800 text-sm mt-1">Presta atención a esta información importante.</p>
    </div>
  </div>
</div>
```

### Alert Info
```html
<div class="bg-blue-50 border border-blue-200 rounded-lg p-4 mb-4">
  <div class="flex items-start gap-3">
    <div class="flex-shrink-0 text-blue-600 font-bold mt-0.5">i</div>
    <div>
      <h3 class="font-semibold text-blue-900">Información</h3>
      <p class="text-blue-800 text-sm mt-1">Aquí hay información adicional que debes saber.</p>
    </div>
  </div>
</div>
```

---

## 6. MODALES Y OVERLAYS

### Modal Simple
```html
<div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
  <div class="bg-white rounded-lg p-8 w-full max-w-md">
    <h2 class="text-2xl font-bold text-gray-900 mb-4">Título Modal</h2>
    <p class="text-gray-600 mb-6">Contenido del modal aquí.</p>
    <div class="flex gap-3 justify-end">
      <button class="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50">Cancelar</button>
      <button class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">Confirmar</button>
    </div>
  </div>
</div>
```

---

## 7. TABLAS

### Tabla Básica
```html
<div class="overflow-x-auto border border-gray-200 rounded-lg">
  <table class="w-full text-sm">
    <thead class="bg-gray-50 border-b border-gray-200">
      <tr>
        <th class="px-6 py-3 text-left font-semibold text-gray-900">Nombre</th>
        <th class="px-6 py-3 text-left font-semibold text-gray-900">Email</th>
        <th class="px-6 py-3 text-left font-semibold text-gray-900">Estado</th>
        <th class="px-6 py-3 text-left font-semibold text-gray-900">Acciones</th>
      </tr>
    </thead>
    <tbody>
      <tr class="border-b border-gray-200 hover:bg-gray-50">
        <td class="px-6 py-4 text-gray-900">Juan Pérez</td>
        <td class="px-6 py-4 text-gray-600">juan@email.com</td>
        <td class="px-6 py-4"><span class="bg-green-100 text-green-800 px-2 py-1 rounded text-xs font-semibold">Activo</span></td>
        <td class="px-6 py-4"><a href="#" class="text-blue-600 hover:text-blue-700 text-xs font-semibold">Editar</a></td>
      </tr>
    </tbody>
  </table>
</div>
```

---

## 8. BADGES Y ETIQUETAS

### Badge Básico
```html
<span class="bg-blue-100 text-blue-800 px-3 py-1 rounded-full text-xs font-semibold">
  Nuevo
</span>
```

### Badge Estado
```html
<span class="bg-green-100 text-green-800 px-3 py-1 rounded-full text-xs font-semibold">✓ Completado</span>
<span class="bg-yellow-100 text-yellow-800 px-3 py-1 rounded-full text-xs font-semibold">⏳ Pendiente</span>
<span class="bg-red-100 text-red-800 px-3 py-1 rounded-full text-xs font-semibold">✕ Cancelado</span>
```

---

## 9. SPINNER Y LOADING

### Spinner Básico
```html
<div class="flex items-center justify-center">
  <div class="w-8 h-8 border-4 border-gray-200 border-t-blue-600 rounded-full animate-spin"></div>
</div>
```

### Loading Skeleton
```html
<div class="bg-white p-6 rounded-lg border border-gray-200">
  <div class="h-4 bg-gray-200 rounded w-3/4 mb-4 animate-pulse"></div>
  <div class="h-4 bg-gray-200 rounded w-full mb-4 animate-pulse"></div>
  <div class="h-4 bg-gray-200 rounded w-5/6 animate-pulse"></div>
</div>
```

---

## 10. HERO SECTION

### Hero Simple
```html
<div class="bg-gradient-to-r from-blue-600 to-blue-800 text-white py-20 px-6 text-center">
  <h1 class="text-5xl font-bold mb-4">Bienvenido</h1>
  <p class="text-xl text-blue-100 mb-8 max-w-2xl mx-auto">Descripción destacada de tu producto o servicio.</p>
  <button class="px-8 py-3 bg-white text-blue-600 font-bold rounded-lg hover:bg-blue-50 transition-colors">
    Comenzar Ahora
  </button>
</div>
```

---

## Cómo Usar Este Skill

**Sin consumir tokens extra**, simplemente:

1. **Di qué necesitas:**
   > "Necesito un formulario de login con email y contraseña"

2. **Yo respondo:**
   > "Aquí está el componente listo para copiar:"

3. **Copias el HTML/CSS** directamente sin explicaciones largas

---

## Variantes Token-Eficientes

En lugar de generar componentes nuevos cada vez, usa las variantes presentes:

- **Botones:** Cambiar `bg-blue-600` por `bg-red-600`, `bg-green-600`, etc.
- **Colores:** Azul principal → rojo/verde/púrpura
- **Tamaños:** `px-6 py-3` → `px-3 py-1.5` (pequeño) o `px-8 py-4` (grande)
- **Estados:** Agregar `opacity-60 cursor-not-allowed` para deshabilitado

---

## Integración Rápida

Si necesitas un componente más complejo, combina:
- Input (formulario) + Botón + Alert
- Card + Tabla + Pagination
- Nav + Hero + Cards en grid

**Todo pre-optimizado para Tailwind CSS v3+**

---

## Notas de Diseño Profesional

- ✅ Colores consistentes (azul como primario)
- ✅ Espaciado uniforme (múltiplos de 4px)
- ✅ Transiciones suaves (200-300ms)
- ✅ Tipografía clara (semibold para títulos)
- ✅ Estados visuales (hover, focus, active)
- ✅ Accesibilidad incluida (focus rings, contraste)

---

## Próximo Paso

Usa este skill cuando necesites UI rápida. Para layouts complejos, usa `responsive-layout-snippets`. Para patrones accesibles, usa `accessible-ui-patterns`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
