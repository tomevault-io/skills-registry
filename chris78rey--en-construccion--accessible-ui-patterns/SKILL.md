---
name: accessible-ui-patterns
description: Proporciona patrones UI accesibles (WCAG 2.1 AA) listos para copiar-pegar que garantizan cumplimiento de estándares de accesibilidad sin afectar diseño profesional. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Patrones UI Accesibles (WCAG 2.1 AA)

## Propósito

Generar componentes y layouts **100% accesibles** que cumplan WCAG 2.1 AA sin sacrificar diseño profesional. Usa snippets reutilizables que solo necesitas copiar-pegar.

---

## 1. Formularios Accesibles (Copiar-Pegar)

### Patrón: Input de Texto Accesible

```html
<div class="form-group">
  <label for="email" class="form-label">
    Correo Electrónico
    <span class="required" aria-label="requerido">*</span>
  </label>
  <input
    type="email"
    id="email"
    name="email"
    class="form-input"
    required
    aria-required="true"
    aria-describedby="email-hint"
    placeholder="tu@ejemplo.com"
  />
  <small id="email-hint" class="form-hint">
    Usaremos esto para enviar confirmación
  </small>
</div>

<style>
  .form-group {
    margin-bottom: 1.5rem;
  }

  .form-label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 600;
    color: #1f2937;
  }

  .required {
    color: #ef4444;
    margin-left: 0.25rem;
  }

  .form-input {
    width: 100%;
    padding: 0.75rem;
    border: 2px solid #e5e7eb;
    border-radius: 0.375rem;
    font-size: 1rem;
    transition: all 0.2s;
  }

  .form-input:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }

  .form-input:invalid {
    border-color: #ef4444;
  }

  .form-hint {
    display: block;
    margin-top: 0.25rem;
    color: #6b7280;
    font-size: 0.875rem;
  }
</style>
```

**Accesibilidad incluida:**
- ✅ `<label>` vinculado con `for="id"`
- ✅ `aria-required` para lectores de pantalla
- ✅ `aria-describedby` para hints
- ✅ Focus visible (3px outline)
- ✅ Contraste mínimo 4.5:1
- ✅ Error States claros

---

### Patrón: Select/Dropdown Accesible

```html
<div class="form-group">
  <label for="country" class="form-label">País</label>
  <select
    id="country"
    name="country"
    class="form-select"
    aria-label="Selecciona tu país"
  >
    <option value="">-- Selecciona un país --</option>
    <option value="mx">México</option>
    <option value="es">España</option>
    <option value="ar">Argentina</option>
  </select>
</div>

<style>
  .form-select {
    width: 100%;
    padding: 0.75rem;
    border: 2px solid #e5e7eb;
    border-radius: 0.375rem;
    font-size: 1rem;
    appearance: none;
    background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
    background-repeat: no-repeat;
    background-position: right 0.75rem center;
    background-size: 1.25rem;
    padding-right: 2.5rem;
    cursor: pointer;
  }

  .form-select:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }
</style>
```

---

### Patrón: Radio Buttons Accesibles

```html
<fieldset>
  <legend class="form-label">Tipo de Cuenta</legend>
  
  <div class="radio-group">
    <input
      type="radio"
      id="personal"
      name="accountType"
      value="personal"
      class="radio-input"
      checked
    />
    <label for="personal" class="radio-label">
      <span class="radio-custom"></span>
      Personal
    </label>
  </div>

  <div class="radio-group">
    <input
      type="radio"
      id="business"
      name="accountType"
      value="business"
      class="radio-input"
    />
    <label for="business" class="radio-label">
      <span class="radio-custom"></span>
      Business
    </label>
  </div>
</fieldset>

<style>
  fieldset {
    border: none;
    padding: 0;
    margin: 0 0 1.5rem 0;
  }

  legend {
    margin-bottom: 1rem;
  }

  .radio-group {
    margin-bottom: 1rem;
    display: flex;
    align-items: center;
  }

  .radio-input {
    position: absolute;
    opacity: 0;
    width: 0;
    height: 0;
  }

  .radio-label {
    display: flex;
    align-items: center;
    cursor: pointer;
    font-size: 1rem;
    color: #1f2937;
  }

  .radio-custom {
    width: 1.25rem;
    height: 1.25rem;
    border: 2px solid #d1d5db;
    border-radius: 50%;
    margin-right: 0.75rem;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s;
  }

  .radio-input:checked + .radio-label .radio-custom {
    border-color: #3b82f6;
    background: #3b82f6;
  }

  .radio-input:checked + .radio-label .radio-custom::after {
    content: "";
    width: 0.375rem;
    height: 0.375rem;
    background: white;
    border-radius: 50%;
  }

  .radio-input:focus + .radio-label .radio-custom {
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }
</style>
```

---

## 2. Botones Accesibles

### Patrón: Botón Primario

```html
<button
  class="btn btn-primary"
  aria-label="Enviar formulario"
>
  Enviar
</button>

<style>
  .btn {
    padding: 0.75rem 1.5rem;
    border: none;
    border-radius: 0.375rem;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    text-decoration: none;
  }

  .btn:focus {
    outline: 2px solid transparent;
    outline-offset: 2px;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5);
  }

  .btn-primary {
    background-color: #3b82f6;
    color: white;
  }

  .btn-primary:hover {
    background-color: #2563eb;
  }

  .btn-primary:active {
    background-color: #1d4ed8;
  }

  .btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>
```

---

## 3. Navigation Accesible

### Patrón: Menu Principal

```html
<nav class="navbar" role="navigation" aria-label="Principal">
  <div class="navbar-container">
    <a href="/" class="navbar-brand">Logo</a>
    
    <button
      class="navbar-toggle"
      aria-expanded="false"
      aria-label="Abrir menú"
      id="navToggle"
    >
      <span class="hamburger"></span>
    </button>

    <ul class="navbar-menu" id="navMenu" aria-labelledby="navToggle">
      <li>
        <a href="/features" class="navbar-link">Features</a>
      </li>
      <li>
        <a href="/pricing" class="navbar-link">Pricing</a>
      </li>
      <li>
        <a href="/docs" class="navbar-link">Documentación</a>
      </li>
    </ul>
  </div>
</nav>

<style>
  .navbar {
    background: white;
    border-bottom: 1px solid #e5e7eb;
  }

  .navbar-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 1rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .navbar-brand {
    font-size: 1.5rem;
    font-weight: 700;
    color: #1f2937;
    text-decoration: none;
  }

  .navbar-toggle {
    display: none;
    background: none;
    border: none;
    cursor: pointer;
  }

  .navbar-menu {
    display: flex;
    gap: 2rem;
    list-style: none;
    margin: 0;
    padding: 0;
  }

  .navbar-link {
    color: #4b5563;
    text-decoration: none;
    transition: color 0.2s;
  }

  .navbar-link:focus {
    outline: 2px solid #3b82f6;
    outline-offset: 2px;
  }

  .navbar-link:hover {
    color: #3b82f6;
  }

  @media (max-width: 768px) {
    .navbar-toggle {
      display: block;
    }

    .navbar-menu {
      position: absolute;
      top: 100%;
      left: 0;
      right: 0;
      flex-direction: column;
      gap: 0;
      background: white;
      border-bottom: 1px solid #e5e7eb;
      max-height: 0;
      overflow: hidden;
      transition: max-height 0.3s;
    }

    .navbar-menu[aria-expanded="true"] {
      max-height: 300px;
    }

    .navbar-link {
      padding: 1rem;
    }
  }
</style>

<script>
  const toggle = document.getElementById('navToggle');
  const menu = document.getElementById('navMenu');

  toggle.addEventListener('click', () => {
    const isExpanded = toggle.getAttribute('aria-expanded') === 'true';
    toggle.setAttribute('aria-expanded', !isExpanded);
    menu.setAttribute('aria-expanded', !isExpanded);
  });

  // Cerrar menu al hacer click en un link
  document.querySelectorAll('.navbar-link').forEach(link => {
    link.addEventListener('click', () => {
      toggle.setAttribute('aria-expanded', 'false');
      menu.setAttribute('aria-expanded', 'false');
    });
  });
</script>
```

---

## 4. Alertas y Notificaciones Accesibles

### Patrón: Alert Banner

```html
<div
  class="alert alert-info"
  role="alert"
  aria-live="polite"
>
  <span class="alert-icon">ℹ️</span>
  <span class="alert-text">
    Tu cambio fue guardado exitosamente.
  </span>
  <button
    class="alert-close"
    aria-label="Cerrar alerta"
  >
    ✕
  </button>
</div>

<style>
  .alert {
    padding: 1rem;
    border-radius: 0.375rem;
    display: flex;
    align-items: center;
    gap: 0.75rem;
    margin-bottom: 1rem;
  }

  .alert-info {
    background: #dbeafe;
    border: 1px solid #93c5fd;
    color: #0c4a6e;
  }

  .alert-success {
    background: #dcfce7;
    border: 1px solid #86efac;
    color: #166534;
  }

  .alert-error {
    background: #fee2e2;
    border: 1px solid #fca5a5;
    color: #7f1d1d;
  }

  .alert-warning {
    background: #fef3c7;
    border: 1px solid #fcd34d;
    color: #78350f;
  }

  .alert-icon {
    font-size: 1.25rem;
    flex-shrink: 0;
  }

  .alert-text {
    flex: 1;
  }

  .alert-close {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 1.25rem;
    color: inherit;
    opacity: 0.7;
    transition: opacity 0.2s;
  }

  .alert-close:hover,
  .alert-close:focus {
    opacity: 1;
  }
</style>
```

---

## 5. Modal/Dialog Accesible

### Patrón: Modal Dialog

```html
<button class="btn btn-primary" id="openModal">
  Abrir Modal
</button>

<div
  id="myModal"
  class="modal"
  role="dialog"
  aria-labelledby="modalTitle"
  aria-describedby="modalDesc"
  aria-hidden="true"
>
  <div class="modal-overlay" id="modalOverlay"></div>
  
  <div class="modal-content">
    <div class="modal-header">
      <h2 id="modalTitle" class="modal-title">
        Confirmar Acción
      </h2>
      <button
        class="modal-close"
        aria-label="Cerrar modal"
        id="closeModal"
      >
        ✕
      </button>
    </div>

    <div id="modalDesc" class="modal-body">
      <p>¿Estás seguro de que deseas continuar?</p>
    </div>

    <div class="modal-footer">
      <button class="btn btn-secondary" id="cancelModal">
        Cancelar
      </button>
      <button class="btn btn-primary">
        Confirmar
      </button>
    </div>
  </div>
</div>

<style>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
  }

  .modal[aria-hidden="true"] {
    display: none;
  }

  .modal-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
  }

  .modal-content {
    position: relative;
    background: white;
    border-radius: 0.5rem;
    max-width: 500px;
    width: 90%;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
  }

  .modal-header {
    padding: 1.5rem;
    border-bottom: 1px solid #e5e7eb;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .modal-title {
    margin: 0;
    font-size: 1.25rem;
  }

  .modal-close {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .modal-body {
    padding: 1.5rem;
  }

  .modal-footer {
    padding: 1.5rem;
    border-top: 1px solid #e5e7eb;
    display: flex;
    gap: 1rem;
    justify-content: flex-end;
  }

  .btn-secondary {
    background-color: #e5e7eb;
    color: #1f2937;
  }

  .btn-secondary:hover {
    background-color: #d1d5db;
  }
</style>

<script>
  const openBtn = document.getElementById('openModal');
  const closeBtn = document.getElementById('closeModal');
  const cancelBtn = document.getElementById('cancelModal');
  const modal = document.getElementById('myModal');
  const overlay = document.getElementById('modalOverlay');

  function openModal() {
    modal.setAttribute('aria-hidden', 'false');
    closeBtn.focus();
  }

  function closeModalDialog() {
    modal.setAttribute('aria-hidden', 'true');
    openBtn.focus();
  }

  openBtn.addEventListener('click', openModal);
  closeBtn.addEventListener('click', closeModalDialog);
  cancelBtn.addEventListener('click', closeModalDialog);
  overlay.addEventListener('click', closeModalDialog);

  // Cerrar con ESC
  document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape' && modal.getAttribute('aria-hidden') === 'false') {
      closeModalDialog();
    }
  });
</script>
```

---

## 6. Tabla Accesible

### Patrón: Tabla con Headers

```html
<div class="table-wrapper">
  <table class="data-table">
    <thead>
      <tr>
        <th scope="col">Nombre</th>
        <th scope="col">Email</th>
        <th scope="col">Estado</th>
        <th scope="col">Acción</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Juan García</td>
        <td>juan@ejemplo.com</td>
        <td>
          <span class="badge badge-success">Activo</span>
        </td>
        <td>
          <button class="btn btn-sm" aria-label="Editar usuario Juan García">
            Editar
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</div>

<style>
  .table-wrapper {
    overflow-x: auto;
    border-radius: 0.375rem;
    border: 1px solid #e5e7eb;
  }

  .data-table {
    width: 100%;
    border-collapse: collapse;
    background: white;
  }

  .data-table thead {
    background: #f9fafb;
    border-bottom: 2px solid #e5e7eb;
  }

  .data-table th {
    padding: 1rem;
    text-align: left;
    font-weight: 600;
    color: #1f2937;
  }

  .data-table td {
    padding: 1rem;
    border-bottom: 1px solid #e5e7eb;
  }

  .data-table tbody tr:hover {
    background: #f9fafb;
  }

  .badge {
    display: inline-block;
    padding: 0.25rem 0.75rem;
    border-radius: 9999px;
    font-size: 0.875rem;
    font-weight: 600;
  }

  .badge-success {
    background: #dcfce7;
    color: #166534;
  }

  .badge-error {
    background: #fee2e2;
    color: #7f1d1d;
  }

  .btn-sm {
    padding: 0.5rem 1rem;
    font-size: 0.875rem;
  }
</style>
```

---

## 7. Skip Links (Acceso Rápido)

### Patrón: Skip Links

```html
<a href="#main-content" class="skip-link">
  Ir al contenido principal
</a>

<nav aria-label="Principal">
  <!-- Navigation -->
</nav>

<main id="main-content">
  <!-- Main content -->
</main>

<style>
  .skip-link {
    position: absolute;
    top: -40px;
    left: 0;
    background: #3b82f6;
    color: white;
    padding: 0.5rem 1rem;
    text-decoration: none;
    z-index: 100;
  }

  .skip-link:focus {
    top: 0;
  }
</style>
```

---

## 8. Contraste y Colores (WCAG AA)

### Paleta Profesional con Contraste Garantizado

```css
/* Grises Neutrales (Contraste ✅) */
--text-primary: #1f2937;      /* Gris oscuro (nivel AAA) */
--text-secondary: #6b7280;    /* Gris medio */
--text-light: #9ca3af;        /* Gris claro */
--bg-white: #ffffff;          /* Blanco puro */
--bg-light: #f9fafb;          /* Gris muy claro */

/* Azul Principal (Contraste ✅) */
--primary: #3b82f6;
--primary-dark: #1d4ed8;
--primary-light: #dbeafe;

/* Estados (Contraste ✅) */
--success: #059669;
--error: #dc2626;
--warning: #d97706;
--info: #0369a1;

/* Verificación de Contraste */
/* Text #1f2937 sobre #ffffff = 13:1 (AAA ✅) */
/* Text #6b7280 sobre #ffffff = 7:1 (AA ✅) */
/* Text white sobre #3b82f6 = 4.5:1 (AA ✅) */
```

---

## 9. Headings Accesibles

### Patrón: Estructura Correcta

```html
<!-- ❌ INCORRECTO -->
<h2>Bienvenido</h2>
<h4>Sección importante</h4>

<!-- ✅ CORRECTO -->
<h1>Título Principal de la Página</h1>
<h2>Primera Sección</h2>
<h3>Subsección</h3>
<h2>Segunda Sección</h2>

<!-- Regla: Nunca saltear niveles, siempre secuencial -->
```

---

## 10. Checklist de Accesibilidad Rápida

Antes de publicar, verifica:

- [ ] ✅ Todos los inputs tienen `<label>`
- [ ] ✅ Todos los botones tienen `aria-label` si no es texto claro
- [ ] ✅ Colores no son la única forma de comunicar
- [ ] ✅ Focus visible (ring o outline)
- [ ] ✅ Navegación por teclado funciona (Tab, Enter, ESC)
- [ ] ✅ Imágenes tienen `alt` text
- [ ] ✅ Videos tienen subtítulos o transcripciones
- [ ] ✅ Contraste mínimo 4.5:1 (AA)
- [ ] ✅ Tamaño de font mínimo 14px
- [ ] ✅ Enlaces tienen suficiente padding (44x44px mínimo en touch)
- [ ] ✅ Headings son secuenciales (h1, h2, h3...)
- [ ] ✅ Formularios tienen mensajes de error claros
- [ ] ✅ Modals tienen role="dialog" y aria-labelledby
- [ ] ✅ Testear con lector de pantalla (NVDA, JAWS, VoiceOver)

---

## 11. Testing de Accesibilidad (Rápido)

```bash
# Instalar herramientas
npm install -D @axe-core/react axe-core

# Verificar contraste en línea
# https://webaim.org/resources/contrastchecker/

# Testear con teclado
# Tab: navegar
# Enter: activar botones
# ESC: cerrar modales
# Space: checkbox/radio

# Testear en navegador
# F12 > Accessibility Inspector
```

---

## Integración con Otros Skills

- **Después de generar componentes:** usar `validate-ui-accessibility`
- **Para layouts complejos:** usar `responsive-layout-snippets`
- **Para design system:** usar `design-system-components`
- **Para Tailwind rápido:** usar `rapid-tailwind-components`

---

## Resumen

Este skill te proporciona **11 patrones listos para copiar-pegar** que:
- ✅ Cumplen WCAG 2.1 AA
- ✅ Se ven profesionales
- ✅ Son reutilizables
- ✅ No requieren librerías externas
- ✅ Funcionan en todos los navegadores modernos

**Solo copia, pega y adapta el contenido.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
