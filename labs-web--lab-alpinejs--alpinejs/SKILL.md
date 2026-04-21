---
name: alpine-js-expert
description: Guide l'agent pour développer avec Alpine.js en suivant les principes de locality of behavior et réactivité déclarative Use when this capability is needed.
metadata:
  author: labs-web
---

# Skill : Alpine.js Expert

## Mission
Maîtriser Alpine.js dans un contexte **production Laravel + Vite**, en suivant les bonnes pratiques d'organisation et de séparation des responsabilités.

## Philosophie Alpine.js

**Locality of Behavior** : Le comportement reste proche de la structure HTML. Privilégier toujours l'approche déclarative (`x-data`, `x-on`) plutôt qu'impérative (`document.querySelector`).

> **Analogie** : "Alpine est à JavaScript ce que Tailwind est au CSS."

## Installation & Configuration

### 🚫 CDN (Uniquement pour tutoriels/prototypes)
```html
<!-- NE PAS utiliser en production Laravel -->
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
```

### ✅ Installation Professionnelle (Laravel + Vite)

#### 1. Installation via NPM
```bash
npm install alpinejs
```

#### 2. Configuration Vite (`resources/js/app.js`)
```javascript
import './bootstrap';
import Alpine from 'alpinejs';

// Exposer Alpine globalement pour usage dans Blade
window.Alpine = Alpine;

// Démarrer Alpine
Alpine.start();
```

#### 3. Template Blade (`resources/views/layouts/app.blade.php`)
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body>
    @yield('content')
</body>
</html>
```

## Organisation du Code Alpine avec Laravel

### ❌ Mauvaise Pratique : Tout dans Blade
```blade
<!-- NE PAS FAIRE : Logique complexe inline -->
<div x-data="{ 
    items: [], 
    search: '',
    async load() { /* 50 lignes de code... */ }
}" x-init="load()">
    <!-- Template complexe -->
</div>
```

### ✅ Bonne Pratique : Composants Séparés

#### Structure Recommandée
```
resources/
├── js/
│   ├── app.js                 # Point d'entrée principal
│   ├── alpine/
│   │   ├── components/        # Composants Alpine réutilisables
│   │   │   ├── articleManager.js
│   │   │   ├── dropdown.js
│   │   │   └── modal.js
│   │   └── stores/           # Stores globaux (si nécessaire)
│   │       └── cart.js
│   └── utils/                # Helpers (debounce, etc.)
└── views/
    └── articles/
        └── index.blade.php   # Template propre
```

#### Définir un Composant (`resources/js/alpine/components/articleManager.js`)
```javascript
export default () => ({
    articles: [],
    searchQuery: '',
    isLoading: false,
    
    async init() {
        await this.loadArticles();
    },
    
    async loadArticles() {
        this.isLoading = true;
        try {
            const response = await fetch('/api/articles');
            this.articles = await response.json();
        } finally {
            this.isLoading = false;
        }
    },
    
    async searchArticles() {
        const response = await fetch(`/api/articles?q=${this.searchQuery}`);
        this.articles = await response.json();
    }
});
```

#### Enregistrer le Composant (`resources/js/app.js`)
```javascript
import Alpine from 'alpinejs';
import articleManager from './alpine/components/articleManager';

// Enregistrer les composants
Alpine.data('articleManager', articleManager);

window.Alpine = Alpine;
Alpine.start();
```

#### Utiliser dans Blade (`resources/views/articles/index.blade.php`)
```blade
@extends('layouts.app')

@section('content')
<div x-data="articleManager" x-init="init()">
    <!-- Template propre et lisible -->
    <input 
        type="text"
        x-model="searchQuery"
        @input.debounce.500ms="searchArticles()">
    
    <div x-show="isLoading">Chargement...</div>
    
    <ul x-show="!isLoading">
        <template x-for="article in articles" :key="article.id">
            <li x-text="article.title"></li>
        </template>
    </ul>
</div>
@endsection
```

## Directives Essentielles

### État et Initialisation
- `x-data="{ key: value }"` : État local
- `x-init="code"` : Exécution à l'initialisation

### Interactivité
- `@click="handler"` : Événement (raccourci de `x-on:`)
- `:attr="value"` : Attribut dynamique (raccourci de `x-bind:`)
- `x-model="var"` : Binding bidirectionnel

### Affichage
- `x-show="condition"` : Toggle `display: none` (CSS)
- `x-if="condition"` : Ajoute/Retire du DOM (sur `<template>`)
- `x-text="value"` : Injecte texte
- `x-html="value"` : Injecte HTML

### Boucles
- `x-for="item in items"` : Itération (sur `<template>`, nécessite `:key`)

### Magic Properties
- `$refs` : Accès aux `x-ref`
- `$watch('prop', callback)` : Observer changements
- `$dispatch('event', data)` : Émettre événement custom
- `$nextTick(callback)` : Attendre fin du rendu

## Intégration Laravel

### Passer Données PHP → Alpine
```blade
<div x-data="{ 
    articles: @json($articles),
    csrfToken: '{{ csrf_token() }}'
}">
</div>
```

### Requêtes AJAX avec CSRF
```javascript
// Dans le composant Alpine
async createArticle(data) {
    const response = await fetch('/api/articles', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'X-CSRF-TOKEN': this.csrfToken
        },
        body: JSON.stringify(data)
    });
    return await response.json();
}
```

### Routes Laravel dans Alpine
```blade
<div x-data="{ 
    apiUrl: '{{ route('api.articles.index') }}'
}" x-init="fetch(apiUrl).then(...)">
</div>
```

## Bonnes Pratiques

### ✅ À Faire
1. **Utiliser Vite** en production Laravel (jamais CDN)
2. **Séparer composants complexes** dans `resources/js/alpine/components/`
3. **Un fichier par composant** réutilisable
4. **Utiliser `:key` dans `x-for`** pour performances
5. **Passer CSRF token** pour requêtes POST/PUT/DELETE
6. **Débounce les recherches** (`@input.debounce.500ms`)
7. **Gérer états de chargement** (UX)

### ❌ À Éviter
1. **Ne pas mélanger jQuery et Alpine**
2. **Ne pas manipuler le DOM directement** (laisser Alpine gérer)
3. **Éviter logique complexe inline** dans Blade
4. **Ne jamais oublier `<template>` avec `x-for` et `x-if`**
5. **Ne pas utiliser CDN en production Laravel**

## Patterns Courants (Voir `examples.md`)

- Dropdown Menu
- Modale avec Formulaire
- Recherche avec Debounce
- Suppression avec Confirmation
- Tabs / Accordion
- Toast Notifications

## Modificateurs Utiles (Voir `cheatsheet.md`)

**Événements** : `.prevent`, `.stop`, `.outside`, `.window`, `.debounce`, `.throttle`  
**Touches** : `.enter`, `.escape`, `.space`, `.ctrl`, `.cmd`  
**Transitions** : `x-transition`, `x-transition.duration.500ms`

## Contexte d'Utilisation

**Idéal pour** :
- Applications server-side (Laravel Blade, PHP)
- Enrichissement progressif HTML
- Interactivité légère (UI components)

**NON idéal pour** :
- SPA complexes avec routing (→ Vue/React)
- Gestion d'état globale massive (→ Vuex/Redux)

## Ressources

- **Doc Officielle** : [alpinejs.dev](https://alpinejs.dev)
- **Cheatsheet** : Voir `cheatsheet.md` du skill
- **Exemples** : Voir `examples.md` du skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labs-web) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
