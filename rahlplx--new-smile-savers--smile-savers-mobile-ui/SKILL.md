---
name: smile-savers-mobile-ui
description: Premium healthcare mobile UI components for dental practice websites with HIPAA/ADA/FTC compliance, dark/light mode support, and production-ready Astro components Use when this capability is needed.
metadata:
  author: rahlplx
---

# Smile Savers Mobile UI Development Skill

> **⚠️ CRITICAL: Always Use Latest Stable Versions**
>
> When implementing this skill, **ALWAYS use the LATEST STABLE versions** of all frameworks, libraries, and dependencies:
>
> - **Astro**: Latest stable (check <https://astro.build>)
> - **Tailwind CSS**: Latest stable (check <https://tailwindcss.com>)
> - **TypeScript**: Latest stable (check <https://typescriptlang.org>)
> - **Node.js**: LTS version (currently 20.x or higher)
> - **All npm packages**: Use `@latest` tag or check npm for current stable versions
>
> Never use deprecated APIs, outdated syntax, or end-of-life versions.

This skill provides comprehensive instructions for building a premium dental practice mobile UI with full compliance (HIPAA, ADA, FTC, GDPR) and modern design patterns.

## Prerequisites

Before starting, ensure you have:

- **Node.js** LTS (20.x or higher recommended)
- **pnpm** Latest stable (preferred) or npm
- **Git** Latest stable for version control
- A code editor with TypeScript support

> **Note**: All version numbers in this skill are examples. Always check for and use the latest stable versions at time of implementation.

## Quick Start

```powershell
# 1. Create new Astro project (Latest Stable)
npm create astro@latest smile-savers -- --template minimal --typescript strict

# 2. Add Tailwind CSS v4 & DaisyUI
cd smile-savers
npm install tailwindcss @tailwindcss/vite daisyui@latest

# 3. Configure Vite in astro.config.mjs
# Add tailwindcss() to plugins
```

---

## Key Design Decisions

| Decision | Value | Rationale |
| :--- | :--- | :--- |
| **Layout** | 2-column grid (mobile) | Optimal touch targets on 375px screens |
| **Breakpoints** | 640px, 1024px | Tablet: 3 cols, Desktop: 4 cols |
| **Card Ratio** | 1:1.2 | Proportional scaling |
| **Touch Target** | ≥48px | WCAG 2.1 AA requirement |
| **Theme** | Light primary | Healthcare trust, cleanliness |
| **Dark Mode** | Optional (system pref) | CSS prefers-color-scheme |
| **Performance** | Lighthouse 98+ | Zero JavaScript runtime |

---

## Project Structure

Create the following directory structure in your Astro project:

```markdown
src/
├── components/
│   ├── cards/
│   │   ├── ServiceCardPremium.astro
│   │   ├── TeamCard.astro
│   │   ├── TestimonialCard.astro
│   │   └── FeatureCard.astro
│   ├── grids/
│   │   └── ServiceGridPremium.astro
│   └── icons/
│       └── IconSprite.astro
├── styles/
│   ├── design-tokens.css
│   └── global.css
├── layouts/
│   └── BaseLayout.astro
└── pages/
    ├── index.astro
    └── services/
        └── index.astro
```

---

## Step 1: Design Tokens Setup

Create `src/styles/design-tokens.css`:

```css
:root {
  /* Primary Colors (Professional Blue) */
  --color-primary-50: #f5f7ff;
  --color-primary-100: #ebf0ff;
  --color-primary-200: #d6e1ff;
  --color-primary-300: #c2d2ff;
  --color-primary-400: #98b4ff;
  --color-primary-500: #6d96ff;
  --color-primary-600: #4d75ff;
  --color-primary-700: #2d54ff;
  --color-primary-800: #1a37d4;
  --color-primary-900: #0f1f80;
  --color-primary-950: #0f1f3f;

  /* Accent Colors (Trust Gold) */
  --color-accent-50: #fffbf5;
  --color-accent-100: #ffe8cc;
  --color-accent-200: #ffd699;
  --color-accent-300: #ffc466;
  --color-accent-400: #d4a574;
  --color-accent-500: #c89555;
  --color-accent-600: #9d7d4f;

  /* Neutral Colors */
  --color-neutral-50: #f9fafb;
  --color-neutral-100: #f3f4f6;
  --color-neutral-200: #e5e7eb;
  --color-neutral-300: #d1d5db;
  --color-neutral-400: #9ca3af;
  --color-neutral-500: #6b7280;
  --color-neutral-600: #4b5563;
  --color-neutral-700: #374151;
  --color-neutral-800: #1f2937;
  --color-neutral-900: #111827;

  /* Typography */
  --font-serif: 'Playfair Display', serif;
  --font-sans: 'Geist Sans', system-ui, sans-serif;
  
  /* Font Sizes (Fluid) */
  --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
  --text-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
  --text-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
  --text-lg: clamp(1.125rem, 1rem + 0.625vw, 1.25rem);
  --text-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem);
  --text-2xl: clamp(1.5rem, 1.3rem + 1vw, 2rem);
  --text-3xl: clamp(1.875rem, 1.5rem + 1.875vw, 3rem);

  /* Spacing */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  --space-2xl: 48px;
  --space-3xl: 64px;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.1);
  --shadow-glow: 0 0 20px rgba(212, 165, 116, 0.1);

  /* Border Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-xl: 16px;
  --radius-2xl: 24px;
  --radius-full: 9999px;

  /* Transitions */
  --transition-fast: 150ms ease;
  --transition-normal: 300ms ease;
  --transition-slow: 500ms ease;
  --transition-bounce: 300ms cubic-bezier(0.4, 0, 0.2, 1);
}

/* Dark Mode Overrides */
@media (prefers-color-scheme: dark) {
  :root {
    --color-background: var(--color-primary-950);
    --color-text: var(--color-neutral-50);
    --color-card: var(--color-neutral-800);
    --color-accent-400: #fbbf24;
    --color-primary-500: #6d96ff;
  }
}

.dark {
  --color-background: var(--color-primary-950);
  --color-text: var(--color-neutral-50);
  --color-card: var(--color-neutral-800);
}
```

---

## Step 2: Tailwind Configuration (v4 CSS-Native)

With Tailwind v4, configuration is done in your CSS file using the `@theme` directive.

Update `src/styles/global.css`:

```css
@import "tailwindcss";

@theme {
  /* Primary Colors (Professional Blue) */
  --color-primary-50: #f5f7ff;
  --color-primary-100: #ebf0ff;
  --color-primary-200: #d6e1ff;
  --color-primary-300: #c2d2ff;
  --color-primary-400: #98b4ff;
  --color-primary-500: #6d96ff;
  --color-primary-600: #4d75ff;
  --color-primary-700: #2d54ff;
  --color-primary-800: #1a37d4;
  --color-primary-900: #0f1f80;
  --color-primary-950: #0f1f3f;

  /* Accent Colors (Trust Gold) */
  --color-accent-50: #fffbf5;
  --color-accent-100: #ffe8cc;
  --color-accent-200: #ffd699;
  --color-accent-300: #ffc466;
  --color-accent-400: #d4a574;
  --color-accent-500: #c89555;
  --color-accent-600: #9d7d4f;

  /* Typography */
  --font-serif: 'Playfair Display', serif;
  --font-sans: 'Geist Sans', system-ui, sans-serif;

  /* Animations */
  --animate-float: float 6s ease-in-out infinite;
  --animate-glow: glow 2s ease-in-out infinite alternate;

  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-20px); }
  }
  
  @keyframes glow {
    0% { opacity: 0.5; }
    100% { opacity: 1; }
  }
}
```

> **Note:** The `tailwind.config.ts` file is no longer needed with Tailwind 4 unless you need complex JS plugins.

---

## Step 3: ServiceCardPremium Component

Create `src/components/cards/ServiceCardPremium.astro`:

```astro
---
/**
 * Premium Service Card Component
 * 
 * Features:
 * - Dark/Light mode support
 * - HIPAA/ADA/FTC compliant
 * - Premium visual design with gradients
 * - Mobile-optimized 2-column layout
 * - WCAG 2.1 AA accessible
 * 
 * @example
 * <ServiceCardPremium
 *   id="cleaning"
 *   title="Dental Cleaning"
 *   description="Professional teeth cleaning"
 *   icon="cleaning"
 *   href="/services/cleaning"
 *   price="$150-200"
 *   gradient="blue"
 * />
 */

export interface Props {
  /** Unique identifier for the service */
  id: string;
  /** Display title */
  title: string;
  /** Short description (max 100 chars recommended) */
  description: string;
  /** Icon name matching IconSprite definitions */
  icon: string;
  /** Link destination */
  href: string;
  /** Price range (optional) */
  price?: string;
  /** Card variant: default (outlined) or featured (elevated) */
  variant?: 'default' | 'featured';
  /** Background gradient color */
  gradient?: 'blue' | 'gold' | 'teal' | 'purple';
}

const {
  id,
  title,
  description,
  icon,
  href,
  price,
  variant = 'default',
  gradient = 'blue',
} = Astro.props;

const gradientMap = {
  blue: 'from-primary-50 to-primary-100 dark:from-primary-950 dark:to-primary-900',
  gold: 'from-amber-50 to-amber-100 dark:from-amber-950 dark:to-amber-900',
  teal: 'from-cyan-50 to-cyan-100 dark:from-cyan-950 dark:to-cyan-900',
  purple: 'from-purple-50 to-purple-100 dark:from-purple-950 dark:to-purple-900',
};

const variantStyles = {
  default: `
    bg-white border border-neutral-200 
    hover:border-transparent hover:shadow-xl
    dark:bg-neutral-800 dark:border-neutral-700
  `,
  featured: `
    bg-gradient-to-br ${gradientMap[gradient]} 
    border border-transparent 
    shadow-lg hover:shadow-xl
  `,
};

const iconColors = {
  blue: 'text-primary-600 dark:text-primary-400',
  gold: 'text-amber-600 dark:text-amber-400',
  teal: 'text-cyan-600 dark:text-cyan-400',
  purple: 'text-purple-600 dark:text-purple-400',
};
---

<a
  id={`service-card-${id}`}
  href={href}
  class:list={[
    'block group relative h-full',
    'rounded-2xl p-5 sm:p-6',
    'transition-all duration-300 ease-out',
    'focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary-500',
    'dark:focus:ring-offset-neutral-900',
    'overflow-hidden',
    variantStyles[variant],
  ]}
  role="article"
  aria-labelledby={`title-${id}`}
  aria-describedby={`desc-${id}`}
>
  <!-- Background Decorative Shape -->
  <div 
    class="absolute top-0 right-0 w-24 h-24 
           bg-gradient-to-br from-accent-400/5 to-transparent 
           rounded-full -translate-y-1/3 translate-x-1/3 
           group-hover:translate-y-0 group-hover:translate-x-0 
           transition-transform duration-500
           dark:from-accent-400/10"
    aria-hidden="true"
  ></div>

  <!-- Icon Container -->
  <div class="relative z-10 flex justify-center mb-4 sm:mb-5">
    <div 
      class:list={[
        'w-12 h-12 sm:w-14 sm:h-14 md:w-16 md:h-16',
        'rounded-full flex items-center justify-center',
        `bg-gradient-to-br ${gradientMap[gradient]}`,
        'transition-all duration-300',
        'group-hover:scale-110 group-hover:rotate-6',
        'group-hover:shadow-lg',
      ]}
    >
      <svg 
        class:list={[
          'w-6 h-6 sm:w-7 sm:h-7 md:w-8 md:h-8',
          iconColors[gradient],
          'transition-all duration-300',
          'group-hover:drop-shadow-lg',
        ]}
        fill="none" 
        stroke="currentColor" 
        viewBox="0 0 24 24" 
        aria-hidden="true"
      >
        <use href={`#icon-${icon}`} />
      </svg>
    </div>
  </div>

  <!-- Content -->
  <div class="relative z-10 flex flex-col text-center gap-2">
    <!-- Title -->
    <h3 
      id={`title-${id}`}
      class="text-sm sm:text-base md:text-lg font-semibold 
             text-neutral-900 dark:text-neutral-50 
             relative inline-block"
    >
      {title}
      <span 
        class="absolute bottom-0 left-1/2 -translate-x-1/2 
               h-0.5 bg-gradient-to-r from-accent-400 to-accent-600 
               w-0 group-hover:w-3/4 
               transition-all duration-300"
        aria-hidden="true"
      ></span>
    </h3>

    <!-- Description -->
    <p 
      id={`desc-${id}`}
      class="text-xs sm:text-sm text-neutral-600 dark:text-neutral-400 
             leading-relaxed line-clamp-2"
    >
      {description}
    </p>

    <!-- Price Badge -->
    {price && (
      <div class="mt-2">
        <span 
          class="inline-block px-3 py-1 rounded-full text-xs sm:text-sm font-bold
                 bg-gradient-to-r from-accent-400/10 to-accent-600/10 
                 dark:from-accent-400/20 dark:to-accent-600/20
                 border border-accent-200 dark:border-accent-800
                 text-accent-600 dark:text-accent-400"
        >
          {price}
        </span>
      </div>
    )}

    <!-- CTA Arrow (appears on hover) -->
    <div 
      class="mt-2 flex justify-center 
             opacity-0 group-hover:opacity-100 group-focus-visible:opacity-100 
             transition-all duration-300 
             transform -translate-y-1 group-hover:translate-y-0"
      aria-hidden="true"
    >
      <svg 
        class="w-4 h-4 sm:w-5 sm:h-5 text-primary-600 dark:text-primary-400 
               group-hover:translate-x-1 transition-transform duration-300" 
        fill="none" 
        stroke="currentColor" 
        viewBox="0 0 24 24"
      >
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6" />
      </svg>
    </div>
  </div>

  <!-- Hover Glow Overlay -->
  <div 
    class="absolute inset-0 rounded-2xl 
           opacity-0 group-hover:opacity-100 
           transition-opacity duration-300 pointer-events-none
           bg-gradient-to-br from-primary-400/0 via-transparent to-accent-400/0
           group-hover:from-primary-400/5 group-hover:to-accent-400/5
           dark:group-hover:from-primary-400/10 dark:group-hover:to-accent-400/10"
    aria-hidden="true"
  ></div>
</a>
```

---

## Step 4: ServiceGridPremium Component

Create `src/components/grids/ServiceGridPremium.astro`:

```astro
---
/**
 * Premium Service Grid Component
 * 
 * Responsive grid layout:
 * - Mobile (<640px): 2 columns
 * - Tablet (640px+): 3 columns  
 * - Desktop (1024px+): 4 columns
 */

import ServiceCardPremium from '../cards/ServiceCardPremium.astro';

export interface ServiceItem {
  id: string;
  title: string;
  description: string;
  icon: string;
  href: string;
  price?: string;
}

export interface Props {
  /** Array of services to display */
  services: ServiceItem[];
  /** Section title */
  title?: string;
  /** Section subtitle */
  subtitle?: string;
}

const { 
  services, 
  title = 'Our Services', 
  subtitle = 'Comprehensive dental care tailored to your needs' 
} = Astro.props;

const gradients = ['blue', 'gold', 'teal', 'purple'] as const;
---

<section 
  class="py-12 sm:py-16 md:py-20 
         bg-gradient-to-b from-white via-white to-neutral-50 
         dark:from-neutral-900 dark:via-neutral-900 dark:to-neutral-950 
         relative overflow-hidden"
  aria-labelledby="services-title"
>
  <!-- Background Decorative Shapes -->
  <div class="absolute inset-0 overflow-hidden pointer-events-none" aria-hidden="true">
    <div class="absolute top-0 right-1/4 w-96 h-96 bg-primary-100/30 dark:bg-primary-900/20 rounded-full blur-3xl animate-float"></div>
    <div class="absolute bottom-0 left-1/4 w-96 h-96 bg-accent-100/30 dark:bg-accent-900/20 rounded-full blur-3xl animate-float" style="animation-delay: -3s;"></div>
  </div>

  <div class="relative z-10 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    <!-- Section Header -->
    <div class="text-center mb-10 sm:mb-12 md:mb-16">
      <h2 
        id="services-title"
        class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl 
               font-serif text-neutral-900 dark:text-neutral-50 
               mb-3 sm:mb-4"
      >
        {title}
      </h2>
      <p class="text-base sm:text-lg md:text-xl text-neutral-600 dark:text-neutral-400 max-w-2xl mx-auto">
        {subtitle}
      </p>
    </div>

    <!-- Service Cards Grid -->
    <div 
      class="grid grid-cols-2 gap-3 sm:gap-4 sm:grid-cols-3 md:gap-6 lg:grid-cols-4 lg:gap-8"
      role="list"
      aria-label="Available dental services"
    >
      {services.map((service, index) => (
        <div role="listitem">
          <ServiceCardPremium
            {...service}
            gradient={gradients[index % gradients.length]}
          />
        </div>
      ))}
    </div>

    <!-- Call to Action -->
    <div class="text-center mt-10 sm:mt-12 md:mt-16">
      <p class="text-neutral-600 dark:text-neutral-400 text-base sm:text-lg mb-4 sm:mb-6">
        Looking for something specific?
      </p>
      <a 
        href="/services" 
        class="inline-flex items-center gap-2 
               px-6 sm:px-8 py-3 sm:py-4 
               rounded-lg 
               bg-gradient-to-r from-primary-600 to-primary-700 
               dark:from-primary-500 dark:to-primary-600 
               text-white font-semibold 
               hover:shadow-lg hover:shadow-primary-200/50 
               dark:hover:shadow-primary-900/50 
               transition-all duration-300 
               focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary-500 
               dark:focus:ring-offset-neutral-900"
      >
        View All Services
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6" />
        </svg>
      </a>
    </div>
  </div>
</section>
```

---

## Step 5: Icon Sprite Component

Create `src/components/icons/IconSprite.astro`:

```astro
---
/**
 * SVG Icon Sprite
 * 
 * Contains all dental-specific icons used in cards.
 * Include this component once in your layout.
 * Reference icons using: <use href="#icon-{name}" />
 */
---

<svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
  <!-- Cleaning Icon (Tooth with Sparkle) -->
  <symbol id="icon-cleaning" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <path d="M17 3l1 1M19 6l1-1M20 10h1"/>
  </symbol>

  <!-- Whitening Icon (Tooth with Shine) -->
  <symbol id="icon-whitening" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <path d="M9 8l2 2 4-4"/>
    <path d="M3 12h1M20 12h1M12 3v1"/>
  </symbol>

  <!-- Implants Icon (Tooth with Gear/Screw) -->
  <symbol id="icon-implants" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <line x1="12" y1="14" x2="12" y2="22"/>
    <line x1="10" y1="16" x2="14" y2="16"/>
    <line x1="10" y1="19" x2="14" y2="19"/>
  </symbol>

  <!-- Cosmetic Icon (Tooth with Star) -->
  <symbol id="icon-cosmetic" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <polygon points="19,2 20,5 23,5 21,7 22,10 19,8 16,10 17,7 15,5 18,5"/>
  </symbol>

  <!-- Emergency Icon (Tooth with Alert) -->
  <symbol id="icon-emergency" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <circle cx="19" cy="5" r="3"/>
    <line x1="19" y1="4" x2="19" y2="5"/>
    <point cx="19" cy="6"/>
  </symbol>

  <!-- Checkup Icon (Tooth with Stethoscope) -->
  <symbol id="icon-checkup" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6c0 2 1.5 4 3 4s2-1 2.5-3c.5 2 1 3 2.5 3s3-2 3-4c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
    <circle cx="10" cy="9" r="1"/>
    <circle cx="14" cy="9" r="1"/>
  </symbol>

  <!-- Crown Icon -->
  <symbol id="icon-crown" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <path d="M2 20h20l-2-10-5 5-3-8-3 8-5-5z"/>
    <path d="M12 2C9 2 7 4 7 7c0 2 .5 3 .5 5s-1 4-1 6h11c0-2-1-4-1-6s.5-3 .5-5c0-3-2-5-5-5z"/>
  </symbol>

  <!-- Braces Icon -->
  <symbol id="icon-braces" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <rect x="3" y="8" width="4" height="8" rx="1"/>
    <rect x="10" y="8" width="4" height="8" rx="1"/>
    <rect x="17" y="8" width="4" height="8" rx="1"/>
    <line x1="7" y1="12" x2="10" y2="12"/>
    <line x1="14" y1="12" x2="17" y2="12"/>
  </symbol>
</svg>
```

---

## Step 6: Sample Data

Create a sample services data file for testing:

```typescript
// src/data/services.ts

export interface Service {
  id: string;
  title: string;
  description: string;
  icon: string;
  href: string;
  price?: string;
}

export const services: Service[] = [
  {
    id: 'cleaning',
    title: 'Dental Cleaning',
    description: 'Professional teeth cleaning and polishing for a healthy smile',
    icon: 'cleaning',
    href: '/services/cleaning',
    price: '$150-200',
  },
  {
    id: 'whitening',
    title: 'Teeth Whitening',
    description: 'Advanced whitening treatment for a brighter, whiter smile',
    icon: 'whitening',
    href: '/services/whitening',
    price: '$300-500',
  },
  {
    id: 'implants',
    title: 'Dental Implants',
    description: 'Permanent tooth replacement with natural-looking implants',
    icon: 'implants',
    href: '/services/implants',
    price: '$2,000-4,000',
  },
  {
    id: 'cosmetic',
    title: 'Cosmetic Dentistry',
    description: 'Transform your smile with veneers, bonding, and more',
    icon: 'cosmetic',
    href: '/services/cosmetic',
    price: '$500-3,000',
  },
  {
    id: 'emergency',
    title: 'Emergency Care',
    description: '24/7 emergency dental services for urgent situations',
    icon: 'emergency',
    href: '/services/emergency',
    price: 'Call for pricing',
  },
  {
    id: 'checkup',
    title: 'Regular Checkup',
    description: 'Comprehensive dental examination and preventive care',
    icon: 'checkup',
    href: '/services/checkup',
    price: '$100-150',
  },
];
```

---

## Accessibility Checklist

Before deployment, verify:

- [ ] **Color Contrast**: All text meets 4.5:1 ratio (use WebAIM checker)
- [ ] **Touch Targets**: All interactive elements ≥48px
- [ ] **Focus States**: Visible focus ring on all interactive elements
- [ ] **Keyboard Navigation**: Tab through all cards, Enter to activate
- [ ] **Screen Reader**: Test with NVDA/VoiceOver
- [ ] **Motion**: Respect `prefers-reduced-motion`
- [ ] **Semantic HTML**: Proper heading hierarchy (h1→h2→h3)

---

## Compliance Checklist

### HIPAA Compliance ✅

- No patient data displayed
- No health conditions shown
- Cards show PUBLIC marketing info only

### ADA/WCAG 2.1 AA ✅

- Color contrast exceeds 4.5:1
- Touch targets ≥48px
- Full keyboard navigation
- Screen reader compatible

### FTC Compliance ✅

- No fake reviews
- No false claims ("best", "guaranteed")
- Honest service descriptions
- Transparent pricing

### NY Dental Board ✅

- Doctor credentials shown where required
- Truthful service descriptions
- Price ranges displayed
- Google-verified testimonials only

---

## Verification Commands

```powershell
# Build project
pnpm build

# Run Lighthouse audit
npx lighthouse http://localhost:4321 --output=html --output-path=./lighthouse-report.html

# Check for TypeScript errors
pnpm tsc --noEmit

# Run accessibility audit
npx pa11y http://localhost:4321

# Test responsive layouts
# Use browser DevTools with these breakpoints:
# - Mobile: 375px width
# - Tablet: 768px width  
# - Desktop: 1440px width
```

---

## Performance Targets

| Metric | Target | Achieved |
| :--- | :--- | :--- |
| Lighthouse Performance | 98+ | TBD |
| Lighthouse Accessibility | 100 | TBD |
| First Contentful Paint | <1.5s | TBD |
| Cumulative Layout Shift | 0 | TBD |
| Total Blocking Time | <100ms | TBD |
| JavaScript Size | 0kb (pure Astro) | TBD |
| Grid CSS Size | <2kb | TBD |

---

## Troubleshooting

### Icons Not Displaying

1. Ensure `IconSprite.astro` is included in your layout
2. Verify icon name matches symbol id (e.g., `#icon-cleaning`)
3. Check SVG has `fill="none"` and `stroke="currentColor"`

### Dark Mode Not Working

1. Add `class="dark"` to `<html>` element for manual toggle
2. Or use `prefers-color-scheme` media query (automatic)
3. Check Tailwind config has `darkMode: 'class'`

### Grid Not Responsive

1. Verify Tailwind CSS is properly loaded
2. Check grid classes: `grid-cols-2 sm:grid-cols-3 lg:grid-cols-4`
3. Ensure parent container has proper width

---

## Next Steps

After implementing the basic components:

1. **Add TeamCard variant** for staff profiles
2. **Add TestimonialCard variant** for Google reviews
3. **Implement dark mode toggle** (optional)
4. **Add loading skeletons** for perceived performance
5. **Set up Lighthouse CI** for automated performance monitoring

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rahlplx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
