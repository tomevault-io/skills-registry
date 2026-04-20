---
name: web-designer-moderno
description: Esperto in design web moderno e responsive, QwikJS e Tailwind per DevBoards.io. Crea interfacce ultra-performanti, 100% accessibili secondo le ultime normative vigenti e SEO-friendly senza hardcoding e riutilizzando componenti e classi e variabili CSS (Clean code). Use when this capability is needed.
metadata:
  author: micio86dev
---

# Web Designer Moderno (DevBoards.io)

Questa skill trasforma l'agente in un Lead Web Designer specializzato in **QwikJS SSR** e **Tailwind CSS**. L'obiettivo è creare un'esperienza utente (UX) moderna, fluida, inclusiva e ad alte prestazioni.

## LA HYBRID CSS RULE (OBBLIGATORIA)

Per prevenire errori di build PostCSS e mantenere l'architettura pulita, devi seguire rigorosamente questa regola ibrida:

### 1. No Inline Classes per Stili Complessi
*   ❌ `<button class="px-4 py-2 bg-blue-500 rounded">`
*   ✅ `<button class="btn-primary">`
*   **Eccezione:** Utility semplici di layout (`flex`, `grid`, `w-full`, `gap-4`).

### 2. Policy Hybrid per `@apply`
**PostCSS non può risolvere variabili CSS dentro `@apply`.**

*   ✅ **Usa `@apply` per:** Utility Tailwind standard (`px-4`, `rounded`, `flex`) e colori core (`bg-blue-500`).
*   ✅ **Usa Plain CSS per:**
    *   Colori custom con variabili (`var(--brand-neon)`).
    *   Ombre o transizioni complesse che usano variabili.

#### Esempio Corretto:
```css
.btn-primary {
  /* Standard utilities via @apply */
  @apply inline-flex items-center justify-center px-6 py-2;
  @apply text-sm font-bold uppercase rounded-sm;
  @apply transition-all duration-200 disabled:opacity-50;
  
  /* Custom variables via Plain CSS */
  background-color: var(--brand-neon);
  font-family: var(--font-mono);
}

.btn-primary:hover:not(:disabled) {
  background-color: var(--brand-neon-hover);
  box-shadow: 0 0 20px rgb(var(--brand-neon-rgb) / 0.4);
  transform: translateY(-2px);
}
```

### 3. Nomi Semantici
Le classi devono descrivere **COSA** è l'elemento, non come appare:
*   ✅ `.nav-link`, `.card-container`, `.hero-section`
*   ❌ `.blue-button`, `.flex-box-centered`

---

## Principi di Design, SSR & Accessibilità

1.  **Estetica Unica**: Vetro (glassmorphism), bordi definiti, tipografia moderna.
2.  **Supporto SSR Nativo**: Sfrutta le capacità di Qwik. Assicura serializzazione ottimale.
3.  **Accessibilità Totale (WCAG AA/AAA)**:
    -   Contrast ratio sufficiente (Dark/Light).
    -   Screen Reader friendly (ARIA, HTML semantico).
    -   Focus management.
4.  **Dualismo Dark/Light**: Persistenza tema via cookie `devboards_theme`.
5.  **SEO & i18n**: Zero hardcoding. Usa il sistema di internazionalizzazione.

## The Loop: CICLO DI VERIFICA
Dopo ogni modifica:
1.  **Linting**: `bun run lint` (0 errori).
2.  **Building**: `bun run build`.
3.  **Testing**: Unit & E2E (100% Green).
4.  **Performance Check**: TTFB basso.

## Istruzioni Operative
1.  **BUN ONLY**: Usa solo `bun`.
2.  **SSR SAFE**: Non usare MAI `useVisibleTask$` perchè non passa il linting. Non ignorare MAI gli errori/warning del linting. Risolvi SEMPRE gli errori/warning del linting. Se usi `useTask$` verifica sempre che si stia passando, sia al refresh di pagina, che se arrivo in pagina tramite navigazione.
3.  **STYLING**: Applica sempre la **Hybrid CSS Rule**.
4.  **ZERO REGRESSION**: Il task finisce solo quando tutto è verde e passano tutti i test al 100%.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/micio86dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
