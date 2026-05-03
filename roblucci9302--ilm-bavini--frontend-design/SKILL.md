---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, or applications. Generates creative, polished code that avoids generic AI aesthetics. Use when this capability is needed.
metadata:
  author: roblucci9302
---

This skill guides creation of distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details, accessibility, and performance.

## Design Thinking

Before coding, understand the context and commit to a BOLD aesthetic direction:
- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc.
- **Constraints**: Technical requirements (framework, performance, accessibility).
- **Differentiation**: What makes this UNFORGETTABLE?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.

---

## Accessibilité (WCAG) - OBLIGATOIRE

L'accessibilité n'est pas optionnelle. Chaque interface doit être utilisable par tous.

### Règles fondamentales

```jsx
// ✅ Icon buttons DOIVENT avoir aria-label
<button aria-label="Fermer le menu">
  <XIcon className="w-5 h-5" />
</button>

// ❌ JAMAIS d'icône seule sans label
<button><XIcon /></button>

// ✅ Form controls avec labels associés
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ✅ OU aria-label si pas de label visible
<input aria-label="Rechercher" type="search" placeholder="Rechercher..." />

// ✅ Éléments interactifs custom = keyboard accessible
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => (e.key === 'Enter' || e.key === ' ') && handleClick()}
>
  Action
</div>

// ❌ JAMAIS de div cliquable sans role et keyboard
<div onClick={handleClick}>Action</div>
```

### Sémantique HTML

```jsx
// ✅ Utiliser les éléments natifs
<button>Action</button>           // Pas <div role="button">
<a href="/page">Lien</a>          // Pas <span onClick>
<nav>...</nav>                    // Pas <div className="nav">
<main>...</main>                  // Contenu principal
<aside>...</aside>                // Contenu secondaire

// ✅ Hiérarchie des titres
<h1>Titre page</h1>               // Un seul par page
  <h2>Section</h2>
    <h3>Sous-section</h3>

// ✅ Skip link pour navigation clavier
<a href="#main-content" className="sr-only focus:not-sr-only">
  Aller au contenu principal
</a>
```

### Classe sr-only (screen reader only)

```jsx
// Tailwind inclut sr-only, sinon:
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

---

## Focus States - OBLIGATOIRE

Tout élément interactif DOIT avoir un état focus visible.

```jsx
// ✅ Pattern standard avec focus-visible
<button className="rounded-lg px-4 py-2
                   focus-visible:outline-none
                   focus-visible:ring-2
                   focus-visible:ring-blue-500
                   focus-visible:ring-offset-2">
  Action
</button>

// ✅ Pour les liens
<a className="underline
              focus-visible:outline-none
              focus-visible:ring-2
              focus-visible:ring-current
              focus-visible:ring-offset-2">
  Lien
</a>

// ✅ Input avec focus
<input className="border rounded-lg px-4 py-2
                  focus:outline-none
                  focus:ring-2
                  focus:ring-primary
                  focus:border-transparent" />

// ❌ JAMAIS outline-none seul
<button className="outline-none">        // INTERDIT
<button className="focus:outline-none">  // INTERDIT sans remplacement

// ✅ Si tu retires outline, TOUJOURS remplacer
<button className="outline-none focus-visible:ring-2 focus-visible:ring-primary">
```

### Pourquoi focus-visible vs focus

```jsx
// focus: s'active au clic ET au clavier
// focus-visible: s'active SEULEMENT au clavier (meilleur UX)

// ✅ Préférer focus-visible pour les boutons
<button className="focus-visible:ring-2">

// ✅ Utiliser focus pour les inputs (toujours visible)
<input className="focus:ring-2">
```

---

## Mobile-First Design - OBLIGATOIRE

**Penser mobile AVANT desktop.** Chaque interface doit être conçue pour fonctionner parfaitement sur un écran de 375px, puis enrichie pour les écrans plus grands.

### Principes fondamentaux

1. **Commencer par mobile** : Écrire le CSS de base pour mobile, ajouter les breakpoints pour enrichir
2. **Contenu d'abord** : Le contenu doit être lisible et accessible sur petit écran
3. **Touch-friendly** : Tout élément interactif doit être facilement cliquable au doigt (min 44x44px)
4. **Performance** : Mobile = souvent connexion lente, optimiser les assets

### Breakpoints Tailwind

```
Base     → Mobile (0-639px)     ← ÉCRIRE LE CSS ICI D'ABORD
sm:      → 640px+               ← Grands phones
md:      → 768px+               ← Tablettes
lg:      → 1024px+              ← Laptops
xl:      → 1280px+              ← Desktops
```

---

## Patterns Mobile-First Concrets

### Navigation

```jsx
// ✅ BON : Navigation adaptative avec accessibilité
function Navigation() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <header className="sticky top-0 z-50 bg-white/80 backdrop-blur-sm">
      <div className="flex items-center justify-between p-4 md:px-8">
        <Logo />

        {/* Mobile: Hamburger */}
        <button
          className="md:hidden p-2 -mr-2 min-h-[44px] min-w-[44px]
                     focus-visible:ring-2 focus-visible:ring-primary"
          onClick={() => setIsOpen(!isOpen)}
          aria-label={isOpen ? "Fermer le menu" : "Ouvrir le menu"}
          aria-expanded={isOpen}
        >
          <MenuIcon className="w-6 h-6" />
        </button>

        {/* Desktop: Full nav */}
        <nav className="hidden md:flex items-center gap-8">
          <NavLinks />
        </nav>
      </div>

      {/* Mobile menu drawer */}
      {isOpen && (
        <nav className="md:hidden border-t p-4 space-y-4">
          <NavLinks vertical />
        </nav>
      )}
    </header>
  );
}
```

### Grilles

```jsx
// ✅ BON : Grille mobile-first
<div className="grid grid-cols-1 gap-4
                sm:grid-cols-2 sm:gap-6
                lg:grid-cols-3 lg:gap-8">
  {items.map(item => <Card key={item.id} {...item} />)}
</div>

// ❌ MAUVAIS : Desktop-first (casse sur mobile)
<div className="grid grid-cols-3 gap-8">
```

### Typographie

```jsx
// ✅ BON : Tailles qui scalent
<h1 className="text-2xl font-bold
               sm:text-3xl
               md:text-4xl
               lg:text-5xl">
  Titre Principal
</h1>

<p className="text-base leading-relaxed md:text-lg">
  Paragraphe avec bonne lisibilité sur tous les écrans.
</p>

// ❌ MAUVAIS : Taille fixe trop grande pour mobile
<h1 className="text-6xl">
```

### Spacing

```jsx
// ✅ BON : Padding adaptatif
<section className="px-4 py-12
                    md:px-8 md:py-16
                    lg:px-12 lg:py-24">

// ✅ BON : Container responsive
<div className="w-full max-w-7xl mx-auto px-4 md:px-8">

// ❌ MAUVAIS : Largeur fixe
<div className="w-[1200px]">
<div style={{ width: '1000px' }}>
```

### Flexbox

```jsx
// ✅ BON : Stack sur mobile, row sur desktop
<div className="flex flex-col gap-4
                md:flex-row md:gap-8 md:items-center">
  <div className="flex-1">Contenu gauche</div>
  <div className="flex-1">Contenu droite</div>
</div>

// ✅ BON : Wrap intelligemment
<div className="flex flex-wrap gap-2">
  {tags.map(tag => <Tag key={tag} label={tag} />)}
</div>
```

### Images

```jsx
// ✅ BON : Images responsive avec dimensions (évite CLS)
<div className="relative aspect-video w-full overflow-hidden rounded-lg">
  <img
    src={src}
    alt={alt}
    width={1280}
    height={720}
    className="absolute inset-0 w-full h-full object-cover"
    loading="lazy"
  />
</div>

// ✅ BON : Image avec priorité (above fold)
<img
  src={heroImage}
  alt="Hero"
  width={1200}
  height={600}
  fetchPriority="high"
  className="w-full h-auto"
/>

// ✅ BON : Image avec taille max
<img
  src={src}
  alt={alt}
  width={400}
  height={300}
  loading="lazy"
  className="w-full max-w-md mx-auto rounded-lg"
/>

// ❌ MAUVAIS : Pas de dimensions (cause CLS)
<img src={src} alt={alt} />
```

---

## Formulaires - COMPLET

### Attributs obligatoires

```jsx
// ✅ BON : Input complet avec tous les attributs
<input
  id="email"
  type="email"              // Type correct pour validation native
  autoComplete="email"      // Autocomplete pour remplissage auto
  inputMode="email"         // Clavier mobile adapté
  required                  // Validation native
  aria-describedby="email-error"
  className="w-full px-4 py-3
             text-base       // Évite le zoom iOS (min 16px)
             rounded-lg border
             focus:ring-2 focus:ring-primary
             aria-[invalid=true]:border-red-500"
/>
<p id="email-error" className="text-red-500 text-sm mt-1">
  {errors.email}
</p>

// ✅ Input sensible (password, code)
<input
  type="password"
  autoComplete="current-password"
  spellCheck={false}        // Désactiver sur champs sensibles
  autoCorrect="off"
  autoCapitalize="off"
/>
```

### Types d'input et autocomplete

```jsx
// ✅ Utiliser les bons types et autocomplete
<input type="email" autoComplete="email" />
<input type="tel" autoComplete="tel" inputMode="tel" />
<input type="text" autoComplete="name" />
<input type="text" autoComplete="given-name" />      // Prénom
<input type="text" autoComplete="family-name" />     // Nom
<input type="text" autoComplete="street-address" />
<input type="text" autoComplete="postal-code" inputMode="numeric" />
<input type="password" autoComplete="new-password" /> // Création
<input type="password" autoComplete="current-password" /> // Login
<input type="text" autoComplete="one-time-code" inputMode="numeric" /> // OTP
```

### Layout de formulaire

```jsx
// ✅ BON : Form layout responsive avec gestion d'erreurs
function ContactForm() {
  const handleSubmit = (e) => {
    e.preventDefault();
    const firstError = document.querySelector('[aria-invalid="true"]');
    firstError?.focus(); // Focus sur première erreur
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4 md:space-y-6">
      <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
        <FormField label="Prénom" name="firstName" autoComplete="given-name" />
        <FormField label="Nom" name="lastName" autoComplete="family-name" />
      </div>
      <FormField label="Email" name="email" type="email" autoComplete="email" />
      <FormField label="Message" name="message" as="textarea" rows={4} />
      <Button type="submit" className="w-full md:w-auto">
        Envoyer
      </Button>
    </form>
  );
}
```

### Ce qu'il ne faut JAMAIS faire

```jsx
// ❌ JAMAIS bloquer le paste (UX hostile)
<input onPaste={(e) => e.preventDefault()} />

// ❌ JAMAIS de pattern trop restrictif sans raison
<input pattern="[0-9]{5}" /> // Empêche formats valides internationaux

// ❌ JAMAIS d'input sans label
<input placeholder="Email" /> // Le placeholder n'est PAS un label
```

---

## Boutons et Éléments Interactifs

```jsx
// ✅ BON : Bouton complet
<button
  type="button"
  className="px-6 py-3
             min-h-[44px]              // Touch-friendly
             text-base font-medium
             rounded-lg
             bg-primary text-white
             hover:bg-primary-dark
             active:scale-95
             transition-transform
             focus-visible:ring-2
             focus-visible:ring-primary
             focus-visible:ring-offset-2
             disabled:opacity-50
             disabled:cursor-not-allowed"
  disabled={isLoading}
>
  {isLoading ? <Spinner /> : 'Action'}
</button>

// ✅ BON : Full-width sur mobile
<button className="w-full md:w-auto px-8 py-3 min-h-[44px]">
  Valider
</button>

// ❌ MAUVAIS : Trop petit pour le touch
<button className="px-2 py-1 text-xs">
```

---

## Animation & Performance

### Respecter les préférences utilisateur

```jsx
// ✅ Respecter prefers-reduced-motion
<div className="motion-safe:animate-fade-in motion-reduce:animate-none">
  Contenu animé
</div>

// ✅ En CSS/Tailwind config
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}

// ✅ Avec Framer Motion
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  transition={{
    duration: prefersReducedMotion ? 0 : 0.3
  }}
/>
```

### Animations performantes

```jsx
// ✅ Animer SEULEMENT transform et opacity (GPU accelerated)
<div className="transition-transform duration-200 hover:scale-105">
<div className="transition-opacity duration-200 hover:opacity-80">

// ❌ ÉVITER transition-all (performance)
<div className="transition-all duration-200">

// ✅ Être spécifique
<div className="transition-[transform,opacity] duration-200">

// ✅ Animations interruptibles (avec spring)
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", stiffness: 300, damping: 30 }}
/>
```

### Focus sur les moments clés

```jsx
// ✅ Page load avec staggered reveals
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ delay: index * 0.1 }}
/>

// ✅ Hover states qui surprennent
<div className="group">
  <img className="transition-transform duration-300
                  group-hover:scale-110 group-hover:rotate-2" />
</div>

// ✅ Scroll-triggered (avec intersection observer)
const ref = useRef();
const isInView = useInView(ref, { once: true });

<motion.div
  ref={ref}
  initial={{ opacity: 0 }}
  animate={isInView ? { opacity: 1 } : {}}
/>
```

---

## Dark Mode

```jsx
// ✅ Définir color-scheme dans le HTML
<html className="dark" style={{ colorScheme: 'dark' }}>

// ✅ Ou en CSS
:root {
  color-scheme: light dark;
}
.dark {
  color-scheme: dark;
}

// ✅ Couleurs explicites avec variants Tailwind
<div className="bg-white text-gray-900
                dark:bg-gray-900 dark:text-gray-100">

<p className="text-gray-600 dark:text-gray-400">
  Texte secondaire
</p>

// ✅ Couleurs explicites sur éléments natifs (select, etc.)
<select className="bg-white text-gray-900
                   dark:bg-gray-800 dark:text-gray-100">

// ✅ Meta tag theme-color dynamique
<meta
  name="theme-color"
  content={isDark ? '#0a0a0a' : '#ffffff'}
/>

// ✅ Borders et shadows adaptés
<div className="border border-gray-200 dark:border-gray-700
                shadow-lg dark:shadow-gray-900/50">
```

---

## Performance Avancée

### Virtualisation des listes

```jsx
// ✅ Virtualiser les listes > 50 items
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }) {
  const parentRef = useRef(null);

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 50,
  });

  return (
    <div ref={parentRef} className="h-[400px] overflow-auto">
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map((virtualItem) => (
          <div
            key={virtualItem.key}
            style={{
              position: 'absolute',
              top: virtualItem.start,
              height: virtualItem.size,
            }}
          >
            <Item data={items[virtualItem.index]} />
          </div>
        ))}
      </div>
    </div>
  );
}

// ❌ JAMAIS mapper des centaines d'items directement
{hugeArray.map(item => <Item {...item} />)}
```

### Images optimisées

```jsx
// ✅ Lazy-load images below fold
<img loading="lazy" src={src} width={400} height={300} />

// ✅ Priority sur images above fold (hero, LCP)
<img fetchPriority="high" src={heroImage} width={1200} height={600} />

// ✅ Avec Next.js Image
<Image
  src={src}
  alt={alt}
  width={800}
  height={600}
  priority={isAboveFold}
  placeholder="blur"
  blurDataURL={blurHash}
/>

// ✅ Preconnect aux CDNs d'images
<link rel="preconnect" href="https://images.unsplash.com" />
```

### Inputs non-contrôlés quand possible

```jsx
// ✅ Préférer uncontrolled pour performance
<input
  ref={inputRef}
  defaultValue={initialValue}
  // Pas de onChange qui re-render à chaque frappe
/>

// Récupérer la valeur au submit
const handleSubmit = () => {
  const value = inputRef.current.value;
};

// ⚠️ Controlled seulement si nécessaire (validation live, formatting)
<input
  value={value}
  onChange={(e) => setValue(e.target.value)}
/>
```

---

## Navigation & État URL

```jsx
// ✅ L'URL reflète l'état (filtres, pagination, tabs)
// Permet le deep-linking et le partage
import { useSearchParams } from 'react-router-dom';

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams();
  const category = searchParams.get('category') || 'all';
  const page = parseInt(searchParams.get('page') || '1');

  const updateFilters = (newCategory) => {
    setSearchParams({ category: newCategory, page: '1' });
  };

  // URL: /products?category=shoes&page=2
}

// ✅ Utiliser <a> ou <Link> pour la navigation
<Link to="/products?category=shoes">Chaussures</Link>

// ❌ JAMAIS onClick seul pour navigation (pas de deep-link)
<div onClick={() => navigate('/page')}>

// ✅ Confirmation sur actions destructives
const handleDelete = () => {
  if (window.confirm('Supprimer définitivement cet élément ?')) {
    deleteItem();
  }
};

// ✅ Ou avec un modal custom
<AlertDialog>
  <AlertDialogTrigger>Supprimer</AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogTitle>Confirmer la suppression</AlertDialogTitle>
    <AlertDialogDescription>
      Cette action est irréversible.
    </AlertDialogDescription>
    <AlertDialogAction onClick={deleteItem}>Confirmer</AlertDialogAction>
    <AlertDialogCancel>Annuler</AlertDialogCancel>
  </AlertDialogContent>
</AlertDialog>
```

---

## Touch & Mobile UX

### Optimisations tactiles

```jsx
// ✅ Désactiver double-tap zoom sur éléments interactifs
<button className="touch-action-manipulation">

// ✅ Contenir le scroll dans les modals/drawers
<div className="overscroll-contain">
  {/* Contenu scrollable du modal */}
</div>

// ✅ Tap highlight intentionnel
<button className="[-webkit-tap-highlight-color:rgba(0,0,0,0.1)]">

// ✅ Espacement généreux entre éléments cliquables
<div className="flex gap-3">  {/* Minimum 12px */}
  <button>A</button>
  <button>B</button>
</div>
```

### Ce qu'il faut faire

- **Espacement généreux** entre les éléments cliquables (éviter les missclicks)
- **Feedback tactile** : `active:scale-95`, `active:bg-opacity-80`
- **États de chargement** visibles (skeleton, spinner)
- **Scroll naturel** : Éviter les scroll horizontaux cachés
- **Bottom sheet** ou **drawer** au lieu de modals complexes sur mobile

### Ce qu'il ne faut JAMAIS faire

```jsx
// ❌ Hover-only interactions (pas de hover sur mobile)
<div className="opacity-0 hover:opacity-100">
  Contenu caché
</div>

// ✅ Alternative accessible
<div className="opacity-100 md:opacity-0 md:hover:opacity-100">

// ❌ Largeurs fixes en pixels
<div className="w-[800px]">
<div style={{ width: '1200px' }}>
<div style={{ minWidth: '900px' }}>

// ❌ Overflow horizontal forcé
<div className="overflow-x-scroll">
  <div className="w-[2000px]">

// ❌ Texte trop petit
<p className="text-xs"> {/* Difficile à lire sur mobile */}

// ❌ Éléments cliquables trop proches
<div className="flex gap-1"> {/* Pas assez d'espace */}
  <button>A</button>
  <button>B</button>
</div>
```

---

## Internationalisation (i18n)

```jsx
// ✅ Utiliser Intl pour les dates (pas de hardcode)
const formatDate = (date, locale = 'fr-FR') => {
  return new Intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }).format(date);
};
// Output: "24 janvier 2026"

// ✅ Utiliser Intl pour les nombres
const formatCurrency = (amount, currency = 'EUR', locale = 'fr-FR') => {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency,
  }).format(amount);
};
// Output: "1 234,56 €"

// ✅ Utiliser Intl pour les pourcentages
const formatPercent = (value, locale = 'fr-FR') => {
  return new Intl.NumberFormat(locale, {
    style: 'percent',
    minimumFractionDigits: 1,
  }).format(value);
};
// Output: "42,5 %"

// ❌ JAMAIS hardcoder les formats
const date = `${day}/${month}/${year}`;  // Pas international
const price = `$${amount}`;              // Pas international
```

---

## Frontend Aesthetics Guidelines

Focus on:
- **Typography**: Choose distinctive fonts. Avoid generic fonts like Arial and Inter. Pair a distinctive display font with a refined body font.
- **Color & Theme**: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents.
- **Motion**: Focus on high-impact moments: page load with staggered reveals, scroll-triggered animations, hover states that surprise.
- **Spatial Composition**: Unexpected layouts. Asymmetry. Overlap. Generous negative space OR controlled density.
- **Backgrounds & Visual Details**: Gradient meshes, noise textures, geometric patterns, dramatic shadows, grain overlays.

NEVER use generic AI aesthetics: overused fonts (Inter, Roboto), cliched purple gradients, predictable layouts, cookie-cutter design.

---

## Font Pairings Recommandés

BAVINI supporte toutes les Google Fonts via le `UniversalFontLoader`. Voici des pairings distinctifs par style.

### Comment utiliser les fonts

```jsx
// Dans le code généré, importer via Google Fonts
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=Instrument+Serif&display=swap" rel="stylesheet">

// Ou en Tailwind config
module.exports = {
  theme: {
    fontFamily: {
      sans: ['Space Grotesk', 'sans-serif'],
      serif: ['Instrument Serif', 'serif'],
    }
  }
}

// CSS variables
:root {
  --font-heading: 'Space Grotesk', sans-serif;
  --font-body: 'Plus Jakarta Sans', sans-serif;
}
```

### 🎯 Pairings par Style

#### Tech / Startup / SaaS
| Heading | Body | Vibe |
|---------|------|------|
| **Space Grotesk** | Plus Jakarta Sans | Moderne, tech, confiance |
| **Outfit** | DM Sans | Clean, friendly, accessible |
| **Syne** | Manrope | Bold, innovant, audacieux |
| **Urbanist** | Work Sans | Géométrique, moderne, pro |

```jsx
// Exemple: Tech Landing
<h1 className="font-['Space_Grotesk'] text-5xl font-bold">
  Build faster with AI
</h1>
<p className="font-['Plus_Jakarta_Sans'] text-lg text-gray-600">
  The future of development starts here.
</p>
```

#### Luxury / Fashion / Premium
| Heading | Body | Vibe |
|---------|------|------|
| **Bodoni Moda** | Plus Jakarta Sans | Haute couture, élégant |
| **Playfair Display** | Lato | Classique luxe, intemporel |
| **Cormorant Garamond** | Raleway | Éditorial, raffiné |
| **DM Serif Display** | DM Sans | Moderne luxe, équilibré |

```jsx
// Exemple: Fashion Brand
<h1 className="font-['Bodoni_Moda'] text-6xl font-light tracking-tight">
  MAISON ÉLÉGANCE
</h1>
<p className="font-['Plus_Jakarta_Sans'] text-sm tracking-[0.2em] uppercase">
  Paris · Milan · New York
</p>
```

#### Éditorial / Magazine / Blog
| Heading | Body | Vibe |
|---------|------|------|
| **Instrument Serif** | Instrument Sans | Contemporain, journalistique |
| **Newsreader** | Source Sans Pro | Lisible, sérieux, pro |
| **Young Serif** | Work Sans | Friendly, accessible, warm |
| **Fraunces** | Outfit | Playful, unique, mémorable |

```jsx
// Exemple: Blog Article
<h1 className="font-['Instrument_Serif'] text-4xl">
  The Art of Mindful Design
</h1>
<p className="font-['Instrument_Sans'] text-lg leading-relaxed">
  In an age of distraction, thoughtful interfaces...
</p>
```

#### Creative / Agency / Portfolio
| Heading | Body | Vibe |
|---------|------|------|
| **Clash Display** | General Sans | Bold, impact, statement |
| **Archivo Black** | Space Grotesk | Industrial, fort, moderne |
| **Syne** | Satoshi | Artistique, avant-garde |
| **Bebas Neue** | Manrope | Poster, graphique, punchy |

```jsx
// Exemple: Agency Hero
<h1 className="font-['Clash_Display'] text-8xl font-bold uppercase">
  WE CREATE
  <span className="block text-stroke">EXPERIENCES</span>
</h1>
```

#### Friendly / App / Consumer
| Heading | Body | Vibe |
|---------|------|------|
| **Lexend** | Lexend | Accessible, lisible, moderne |
| **Nunito** | Nunito | Rounded, friendly, app |
| **Albert Sans** | Albert Sans | Clean, versatile, pro |
| **Bricolage Grotesque** | Plus Jakarta Sans | Quirky, fun, mémorable |

```jsx
// Exemple: Mobile App
<h2 className="font-['Lexend'] text-2xl font-semibold">
  Welcome back! 👋
</h2>
<p className="font-['Lexend'] text-base text-gray-500">
  You have 3 new notifications
</p>
```

#### Brutalist / Experimental
| Heading | Body | Vibe |
|---------|------|------|
| **Space Mono** | Space Grotesk | Tech brutalist, raw |
| **Darker Grotesque** | IBM Plex Mono | Dark, condensed, edgy |
| **Big Shoulders Display** | Work Sans | Industrial, American |
| **Unbounded** | Outfit | Rounded brutalist, soft |

```jsx
// Exemple: Brutalist
<h1 className="font-['Space_Mono'] text-4xl uppercase tracking-tighter">
  [ERROR_404]
</h1>
<p className="font-['Space_Grotesk'] text-mono">
  PAGE_NOT_FOUND.exe
</p>
```

### ❌ Fonts à ÉVITER (trop génériques)

Ces fonts sont **supportées** mais créent des designs "AI slop" :

| Font | Problème | Alternative |
|------|----------|-------------|
| **Inter** | Surused, défaut de tout | Space Grotesk, Outfit, Plus Jakarta Sans |
| **Roboto** | Google default, sans personnalité | Manrope, Work Sans, DM Sans |
| **Open Sans** | Safe mais ennuyeux | Nunito, Lexend, Albert Sans |
| **Arial** | System font, zéro caractère | N'importe quelle Google Font |
| **Helvetica** | Classique mais cliché | Switzer, Supreme, Urbanist |

### 🎨 Font Stacks Recommandés

```css
/* Tech/Modern */
--font-heading: 'Space Grotesk', 'Outfit', system-ui, sans-serif;
--font-body: 'Plus Jakarta Sans', 'DM Sans', system-ui, sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', ui-monospace, monospace;

/* Editorial/Luxury */
--font-heading: 'Instrument Serif', 'Playfair Display', Georgia, serif;
--font-body: 'Instrument Sans', 'Source Sans Pro', system-ui, sans-serif;

/* Friendly/App */
--font-heading: 'Lexend', 'Nunito', system-ui, sans-serif;
--font-body: 'Lexend', 'Nunito', system-ui, sans-serif;

/* Brutalist/Experimental */
--font-heading: 'Space Mono', 'JetBrains Mono', monospace;
--font-body: 'Space Grotesk', 'IBM Plex Sans', sans-serif;
```

### 📏 Règles de Pairing

1. **Contraste** : Pairer serif heading + sans body (ou inverse)
2. **Cohérence** : Même "famille" de design (géométrique + géométrique)
3. **Hiérarchie** : Display bold pour titres, regular pour body
4. **Maximum 2-3 fonts** par projet (heading, body, optionnel mono)
5. **Tester les poids** : Charger seulement 400, 500, 700 (pas tous)

---

## Typographie Avancée

```jsx
// ✅ Utiliser ellipsis (…) pas trois points (...)
<span>Voir plus…</span>

// ✅ Utiliser les guillemets français
<q>« Citation française »</q>

// ✅ Espaces insécables avant ponctuation double
<span>Question&nbsp;?</span>
<span>10&nbsp;000&nbsp;€</span>

// ✅ tabular-nums pour colonnes de chiffres
<td className="tabular-nums text-right">1 234,56</td>
<td className="tabular-nums text-right">987,00</td>

// ✅ Gérer le text overflow
<p className="truncate">Texte très long...</p>
<p className="line-clamp-3">Texte limité à 3 lignes...</p>
```

---

## Anti-Patterns à Éviter

Liste des patterns problématiques à ne JAMAIS utiliser :

| Anti-Pattern | Problème | Solution |
|--------------|----------|----------|
| `user-scalable=no` | Bloque le zoom (accessibilité) | Retirer |
| `transition-all` | Performance (recalcul de tout) | Spécifier les propriétés |
| `outline-none` seul | Pas de focus visible | Ajouter `ring` |
| `<div onClick>` | Pas accessible clavier | Ajouter `role`, `tabIndex`, `onKeyDown` |
| `<img>` sans dimensions | Cause CLS (layout shift) | Ajouter `width`/`height` |
| `.map()` sur >50 items | Performance | Virtualiser |
| `<input>` sans label | Pas accessible | Ajouter `<label>` ou `aria-label` |
| `<button>` icône seule | Pas accessible | Ajouter `aria-label` |
| `onPaste={preventDefault}` | UX hostile | Retirer |
| Placeholder comme label | Pas accessible | Ajouter vrai label |
| `!important` abusif | Maintenance difficile | Spécificité CSS |
| Inline styles | Pas maintenable | Classes Tailwind |

---

## Checklist Finale

Avant de livrer une interface :

### Accessibilité
- [ ] **Labels** : Tous les inputs ont un label ou aria-label
- [ ] **Focus** : Tous les éléments interactifs ont un focus visible
- [ ] **Keyboard** : Navigation possible sans souris
- [ ] **ARIA** : Icônes/boutons icônes ont aria-label
- [ ] **Headings** : Hiérarchie h1 > h2 > h3 respectée

### Responsive
- [ ] **Mobile** : Testé sur viewport 375px, tout est lisible et cliquable
- [ ] **Tablet** : Layout adapté, pas de contenu écrasé
- [ ] **Desktop** : Utilise bien l'espace disponible
- [ ] **Touch** : Tous les boutons/liens font minimum 44x44px
- [ ] **Scroll** : Pas de scroll horizontal non voulu

### Performance
- [ ] **Images** : Toutes ont width/height, lazy-load below fold
- [ ] **Listes** : Virtualisées si > 50 items
- [ ] **Animations** : Utilisent transform/opacity, respectent reduced-motion
- [ ] **Assets** : Pas d'assets énormes sur mobile

### Qualité
- [ ] **Texte** : Minimum 16px pour le body, titres lisibles
- [ ] **Forms** : Autocomplete, types corrects, erreurs inline
- [ ] **Dark mode** : Fonctionne si supporté
- [ ] **URLs** : État reflété dans l'URL (filtres, pagination)

**Une interface qui casse sur mobile ou n'est pas accessible est une interface ratée, peu importe sa beauté sur desktop.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roblucci9302) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
