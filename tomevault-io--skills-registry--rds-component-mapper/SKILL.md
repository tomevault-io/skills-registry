---
name: rds-component-mapper
description: Maps visual descriptions, design context, or screenshots to the closest @rds-vue-ui/* design system components. Use when you need to select RDS components for a landing page section based on visual intent, Figma data, or natural-language descriptions. Use when this capability is needed. Use when this capability is needed.
metadata:
  author: tomevault-io
---

# RDS Component Mapper

## 1. Overview

This skill is the **component selection engine** for the RDS (Reusable Design System) Vue UI library. Given a visual description, Figma design context, screenshot analysis, or section type, it maps to the best-matching `@rds-vue-ui/*` component(s).

The RDS library contains **94 components across 15 categories**:

| Category | Count | Examples |
|----------|-------|----------|
| Sections | 22 | Hero, testimonials, parallax, cards, video |
| Cards | 14 | Image, testimonial, article, degree, icon |
| Forms | 11 | Input, checkbox, radio, dropdown, date |
| Core | 8 | Modal, alert, accordion, tooltip, popover |
| Carousels | 5 | Image, tab, ranking, timer sliders |
| Images | 4 | Landscape, portrait, infographic, testimonial |
| Buttons | 1 | Play button |
| Footers | 2 | Standard, partner |
| Navbars | 3 | Sticky/fixed navigation |
| Accordions | 3 | Collapse, overlap, side panel |
| Lists | 3 | Circular, hover, timeline |
| Videos | 2 | Caption, modal |
| Tabs/Tables | 2 | Tab switcher, data table |
| Utilities | 6 | Breadcrumb, loader, analytics, highlight |
| Themes | 7 | Base, Apollo, Atlas, + 4 branded |

All components are installed from the private registry (`https://npm.edpl.us`) as `@rds-vue-ui/{package-name}` and are **auto-imported** via the Nuxt component scanner — no manual import statements are needed.

---

## 2. Component Selection Decision Tree

Use this decision tree to map a visual intent or section description to the correct RDS component. Work top-down: match the **section type** first, then refine by **variant/theme**.

### Heroes

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Full-width hero with background image & gradient overlay | `hero-standard-apollo` | `HeroStandardApollo` |
| Article-style hero with headline and metadata | `hero-article-atlas` | `HeroArticleAtlas` |
| Hero section with embedded video | `hero-video-apollo` | `HeroVideoApollo` |

### Sections — Content Containers

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Generic content section / container | `section-apollo` | `SectionApollo` |
| Section displaying cards in a grid | `section-card-apollo` | `SectionCardApollo` |
| Atlas-themed card section layout | `section-card-atlas` | `SectionCardAtlas` |
| Flexible container with Atlas styling | `section-container-atlas` | `SectionContainerAtlas` |
| Exploration section with interactive elements | `section-explore-atlas` | `SectionExploreAtlas` |
| Grid-based layout for items | `section-grid-atlas` | `SectionGridAtlas` |
| Introduction / featured content section | `section-intro-falcon` | `SectionIntroFalcon` |
| Section with overlapping / layered design | `section-overlap-apollo` | `SectionOverlapApollo` |
| Paginated section with navigation | `section-paginated-atlas` | `SectionPaginatedAtlas` |
| Section with search/filter functionality | `section-search-atlas` | `SectionSearchAtlas` |
| Step-by-step process or timeline | `section-step-apollo` | `SectionStepApollo` |

### Sections — Parallax

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Parallax scrolling effect section | `section-parallax-apollo` | `SectionParallaxApollo` |
| Atlas parallax variant | `section-parallax-atlas` | `SectionParallaxAtlas` |
| Stacked parallax layers | `section-parallax-stacked` | `SectionParallaxStacked` |

### Sections — Testimonials

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Testimonial / quote section (primary) | `section-testimonial-falcon` | `SectionTestimonialFalcon` |
| Testimonial display (Atlas variant) | `section-testimonial-atlas` | `SectionTestimonialAtlas` |
| Multi-column testimonial layout | `section-testimonial-columns` | `SectionTestimonialColumns` |
| Delta variant testimonial section | `section-testimonial-delta` | `SectionTestimonialDelta` |
| Scout variant testimonials | `section-testimonial-scout` | `SectionTestimonialScout` |
| Testimonial with video | `sectional-testimonial-video` | `SectionalTestimonialVideo` |

### Sections — Video

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Video playback section | `section-video-apollo` | `SectionVideoApollo` |
| Section triggering video in modal | `section-video-modal` | `SectionVideoModal` |

### Cards

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Article-style card with image | `card-image-article` | `CardImageArticle` |
| Full-image card with overlay content | `card-image-full` | `CardImageFull` |
| Tile-style card with background image | `card-image-tile` | `CardImageTile` |
| Icon-based feature card | `card-icon` | `CardIcon` |
| Degree program search card | `card-degree-search` | `CardDegreeSearch` |
| Degree information card with text | `card-degree-text` | `CardDegreeText` |
| Testimonial card (Apollo) | `card-testimonial-apollo` | `CardTestimonialApollo` |
| Testimonial card (Atlas) | `card-testimonial-atlas` | `CardTestimonialAtlas` |
| Horizontal info card | `card-info-horizontal` | `CardInfoHorizontal` |
| Vertical info card | `card-info-vertical` | `CardInfoVertical` |
| Link-based Atlas card | `card-link-atlas` | `CardLinkAtlas` |
| Text-focused Atlas card | `card-text-atlas` | `CardTextAtlas` |
| Card for carousel/slider | `carousel-card-apollo` | `CarouselCardApollo` |
| Hover-interactive Starbucks-themed card | `starbucks-hover-card` | `StarbucksHoverCard` |

### Accordions & Collapsibles

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Overlapping accordion design (primary) | `overlap-accordion-atlas` | `OverlapAccordionAtlas` |
| Core accordion component | `rds-accordion` | `RdsAccordion` |
| Single collapsible item | `collapse-item` | `CollapseItem` |
| Side panel with accordion | `side-panel-accordion` | `SidePanelAccordion` |

### Carousels & Sliders

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Image carousel/slider | `carousel-image-apollo` | `CarouselImageApollo` |
| Carousel with tab navigation | `tab-carousel-atlas` | `TabCarouselAtlas` |
| Ranking/scoring carousel | `ranking-carousel-apollo` | `RankingCarouselApollo` |
| Time-based carousel rotation | `timer-carousel-apollo` | `TimerCarouselApollo` |

### Navigation

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Sticky navigation bar | `navbar-sticky-apollo` | `NavbarStickyApollo` |
| Sticky navigation (Atlas variant) | `navbar-sticky-atlas` | `NavbarStickyAtlas` |
| Fixed positioning navbar | `navbar-fixed-atlas` | `NavbarFixedAtlas` |
| Breadcrumb navigation | `breadcrumb-apollo` | `BreadcrumbApollo` |

### Footers

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Standard footer with links and branding | `footer-standard` | `FooterStandard` |
| Partner/sponsor footer layout | `footer-partner` | `FooterPartner` |

### Videos

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Video with caption/subtitle display | `video-caption-apollo` | `VideoCaptionApollo` |
| Video triggered in a modal dialog | `video-modal-atlas` | `VideoModalAtlas` |

### Tabs & Tables

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Tab-based content switching | `tab-switcher-apollo` | `TabSwitcherApollo` |
| Data table/grid | `table-apollo` | `TableApollo` |

### Forms

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Text input with autocomplete | `typeinput-text` | `TypeinputText` |
| Dropdown / select | `dropdown-apollo` | `DropdownApollo` |
| Core dropdown item | `rds-dropdown` | `RdsDropdownItem` |
| Checkbox | `form-checkbox` | `FormCheckbox` |
| Radio button (single) | `radio-button-apollo` | `RadioButtonApollo` |
| Radio button group | `radio-group-apollo` | `RadioGroupApollo` |
| Date picker | `date-picker` | `DatePicker` |
| Search with autocomplete suggestions | `typeahead-search` | `TypeaheadSearch` |
| Select with typeahead filtering | `typeahead-select` | `TypeaheadSelect` |
| File upload input | `file-input-apollo` | `FileInputApollo` |
| Phone number input with formatting | `phone-input-apollo` | `PhoneInputApollo` |

### Lists

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Timeline / vertical list display | `list-timeline` | `ListTimeline` |
| Circular list item display | `list-item-circular` | `ListItemCircular` |
| Hover-interactive list item | `list-item-hover` | `ListItemHover` |

### Images

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Landscape image display | `image-landscape-apollo` | `ImageLandscapeApollo` |
| Portrait image display | `image-portrait-apollo` | `ImagePortraitApollo` |
| Infographic image | `image-infographic-apollo` | `ImageInfographicApollo` |
| Testimonial image | `image-testimonial-apollo` | `ImageTestimonialApollo` |

### Buttons

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Play button for media | `button-play-apollo` | `ButtonPlayApollo` |

### Core UI

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Alert / notification | `rds-alert` | `RdsAlert` |
| Modal / dialog | `rds-modal` | `RdsModal` |
| Off-canvas side panel | `rds-offcanvas` | `RdsOffcanvas` |
| Pagination controls | `rds-pagination` | `RdsPagination` |
| Popover tooltip | `rds-popover` | `RdsPopover` |
| Simple tooltip | `rds-tool-tip` | `RdsToolTip` |
| Loading spinner | `rds-loader` | `RdsLoader` |

### Utilities

| Visual Intent | Component | Vue Name |
|---------------|-----------|----------|
| Text/content highlight | `highlight-apollo` | `HighlightApollo` |
| Slide-in animation content | `content-slide-in-atlas` | `ContentSlideInAtlas` |
| Content with timed visibility | `content-timed-delay` | `ContentTimedDelay` |
| Google Analytics events (composable) | `analytics-gs-composable` | N/A (use `useAnalytics()`) |

### Quick-Match Reference

For the most common visual patterns:

```
Full-width hero with background image  → hero-standard-apollo
Content section / generic container    → section-apollo
Testimonial / quote section            → section-testimonial-falcon
Accordion / FAQ section                → overlap-accordion-atlas or rds-accordion
Image cards in a grid                  → card-image-article or card-image-full
Testimonial cards                      → card-testimonial-apollo
Degree/program cards                   → card-degree-text or card-degree-search
Icon feature cards                     → card-icon
Ranking/stat display                   → ranking-carousel-apollo
Image carousel/slider                  → carousel-image-apollo
Tab-based content switching            → tab-switcher-apollo or tab-carousel-atlas
Video with captions                    → video-caption-apollo
Video in modal                         → video-modal-atlas
Standard page footer                   → footer-standard
Partner/co-branded footer              → footer-partner
Sticky navigation                      → navbar-sticky-apollo
Fixed navigation                       → navbar-fixed-atlas
Form text input                        → typeinput-text
Form dropdown                          → dropdown-apollo
Form checkbox                          → form-checkbox
Form radio buttons                     → radio-group-apollo
Phone input                            → phone-input-apollo
Date picker                            → date-picker
File upload                            → file-input-apollo
Search with autocomplete               → typeahead-search or typeahead-select
Alert/notification                     → rds-alert
Modal/dialog                           → rds-modal
Breadcrumb navigation                  → breadcrumb-apollo
Loading spinner                        → rds-loader
Parallax section                       → section-parallax-apollo
Step-by-step process                   → section-step-apollo
Timeline/history                       → list-timeline
```

---

## 3. Prop Mapping Guide

### Common Props Pattern (all components)

| Prop | Type | Purpose |
|------|------|---------|
| `modelValue` (forms only) | Any | Two-way binding for form inputs via `v-model` |
| `variant` | string | Visual style/theme variant |
| `size` | string | Component size (`xs`, `sm`, `md`, `lg`, `xl`) |
| `disabled` | boolean | Disable interaction |
| `title` | string | Component heading |
| `description` | string | Secondary text |

### Hero Components

**`hero-standard-apollo`** — Full-width hero with image background and gradient overlay.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `bgImageSource` | string | ✓ | URL or path to background image |
| `title` | string | ✓ | Main heading text (rendered in `<h1>`) |
| `displayGradient` | boolean | | Show gradient overlay on image (default: `true`) |
| `fullWidth` | boolean | | Stretch hero to full viewport width |
| `gradientOpacity` | number | | Opacity of gradient overlay (0–1) |

Slots: `#default` (main content), `#cta` (call-to-action area), `#overlay` (overlay content).

**`hero-article-atlas`** — Article-style hero with headline and metadata.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | ✓ | Headline text |
| `subtitle` | string | | Subheadline |
| `imageSource` | string | | Hero image URL |

**`hero-video-apollo`** — Hero section with embedded video.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `videoSource` | string | ✓ | Video URL or embed path |
| `title` | string | | Overlay title |
| `posterImage` | string | | Poster image shown before playback |

### Sections

**`section-apollo`** — Basic container section. The most commonly used wrapper.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | | Section heading |
| `containerClass` | string | | CSS class for the inner container |

Slots: `#default` (section content). Use freely for any body HTML/components.

**`section-card-apollo`** — Section displaying cards in a grid.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `cards` | array | ✓ | Array of card data objects |
| `title` | string | | Section heading |
| `columns` | number | | Number of grid columns |

**`section-testimonial-falcon`** — Testimonial showcase section.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `testimonials` | array | ✓ | Array of testimonial objects |
| `title` | string | | Section heading |

**`section-parallax-apollo`** — Parallax scrolling effect section.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `imageSource` | string | ✓ | Background image for parallax |
| `title` | string | | Overlay heading |
| `scrollSpeed` | number | | Parallax scroll speed multiplier |

**`section-step-apollo`** — Step-by-step process section.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `steps` | array | ✓ | Array of step objects |
| `currentStep` | number | | Currently active step |

**`section-intro-falcon`** — Introduction / featured content section.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | ✓ | Main heading |
| `subtitle` | string | | Secondary heading |
| `content` | string | | Body content (HTML supported) |

### Accordions

**`overlap-accordion-atlas`** — Overlapping accordion design (primary FAQ/accordion component).

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `items` | array | ✓ | Array of `{ title, body, accordionId, image? }` |
| `title` | string | | Section heading above accordion |
| `displayLabel` | string | | Label displayed above items |

Each item in `items`:
```json
{
  "title": "Accordion heading",
  "body": "<p>HTML content for the expanded panel</p>",
  "accordionId": "unique-id",
  "image": "images/optional.png"
}
```

**`rds-accordion`** — Core accordion component.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `items` | array | ✓ | Array of accordion item objects |
| `multiOpen` | boolean | | Allow multiple items open simultaneously |
| `activeItem` | string | | ID of the initially open item |

### Cards

**`card-image-article`** — Article-style card with image.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `imageSource` | string | ✓ | Card image URL |
| `title` | string | ✓ | Card heading |
| `description` | string | | Card body text |
| `publishDate` | string | | Publication date |

**`card-image-full`** — Full-image card with overlay content.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `bgImageSrc` | string | ✓ | Background image URL |
| `title` | string | ✓ | Overlay heading |
| `displayGradient` | boolean | | Show gradient overlay |

**`card-icon`** — Icon-based feature card.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `iconSource` | string | ✓ | Icon image or class |
| `title` | string | ✓ | Card heading |
| `description` | string | | Card body text |

**`card-testimonial-apollo`** — Testimonial card.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `quote` | string | ✓ | Testimonial text |
| `author` | string | ✓ | Author name |
| `authorTitle` | string | | Author title/role |
| `image` | string | | Author photo URL |

**`card-degree-text`** — Degree information card.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | ✓ | Degree name |
| `description` | string | | Degree description |
| `label` | string | | Category label |

**`card-info-horizontal`** — Horizontal layout info card.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | ✓ | Card heading |
| `description` | string | | Card body text |
| `imagePosition` | string | | `left` or `right` |

**`card-info-vertical`** — Vertical layout info card.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | ✓ | Card heading |
| `description` | string | | Card body text |
| `iconSource` | string | | Icon/image URL |

### Carousels

**`carousel-image-apollo`** — Image carousel/slider.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `images` | array | ✓ | Array of image URLs or objects |
| `autoScroll` | boolean | | Enable auto-scrolling |
| `showNavigation` | boolean | | Show prev/next controls |

**`ranking-carousel-apollo`** — Ranking/scoring carousel.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `rankings` | array | ✓ | Array of ranking data objects |
| `showScores` | boolean | | Display numeric scores |
| `animateOnScroll` | boolean | | Animate on scroll into view |

**`tab-carousel-atlas`** — Carousel with tab navigation.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `items` | array | ✓ | Content items |
| `tabs` | array | ✓ | Tab label/identifier array |
| `activeTab` | string | | Initially active tab |

### Navigation

**`navbar-sticky-apollo`** — Sticky navigation bar.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `logo` | string | | Logo image URL |
| `menuItems` | array | ✓ | Array of `{ label, href }` objects |
| `sticky` | boolean | | Enable sticky positioning (default: `true`) |

**`navbar-fixed-atlas`** — Fixed positioning navbar.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `menuItems` | array | ✓ | Array of menu item objects |
| `fixedTop` | boolean | | Fix to top of viewport |
| `transparent` | boolean | | Transparent background |

### Footers

**`footer-standard`** — Standard footer with links and branding.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `links` | array | | Array of footer link objects |
| `copyrightText` | string | | Copyright line text |
| `socialLinks` | array | | Array of social media link objects |

Primarily uses **slots** for content injection. Minimal props needed.

**`footer-partner`** — Partner/sponsor footer.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `partners` | array | | Array of partner data objects |
| `partnerLogos` | array | | Array of logo URLs |
| `description` | string | | Partnership description |

### Forms

**`typeinput-text`** — Text input with autocomplete.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | string | ✓ | Bound value via `v-model` |
| `placeholder` | string | | Placeholder text |
| `suggestions` | array | | Autocomplete suggestion list |

**`dropdown-apollo`** — Dropdown/select component.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | any | ✓ | Selected value via `v-model` |
| `options` | array | ✓ | Array of `{ value, text }` option objects |
| `placeholder` | string | | Default placeholder text |

**`form-checkbox`** — Styled checkbox.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | boolean | ✓ | Checked state via `v-model` |
| `label` | string | ✓ | Checkbox label text |
| `disabled` | boolean | | Disable the checkbox |

**`radio-group-apollo`** — Group of radio buttons.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | any | ✓ | Selected value via `v-model` |
| `options` | array | ✓ | Array of `{ value, label }` objects |
| `name` | string | ✓ | Form field group name |

**`date-picker`** — Date input picker.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | string/Date | ✓ | Selected date via `v-model` |
| `minDate` | string/Date | | Minimum selectable date |
| `maxDate` | string/Date | | Maximum selectable date |

**`phone-input-apollo`** — Phone number input with formatting.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `modelValue` | string | ✓ | Phone number via `v-model` |
| `countryCode` | string | | Default country code |
| `placeholder` | string | | Placeholder text |

**`file-input-apollo`** — File upload input.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `accept` | string | | Accepted MIME types |
| `multiple` | boolean | | Allow multiple files |
| `maxFileSize` | number | | Max file size in bytes |

### Core UI

**`rds-alert`** — Alert notification.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | ✓ | Alert type: `success`, `warning`, `danger`, `info` |
| `message` | string | ✓ | Alert message text |
| `dismissible` | boolean | | Show dismiss/close button |
| `title` | string | | Alert heading |

**`rds-modal`** — Modal dialog.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `open` | boolean | ✓ | Control visibility via `v-model` |
| `title` | string | | Modal heading |
| `size` | string | | `sm`, `md`, `lg`, `xl` |
| `hasFooter` | boolean | | Render footer slot |

**`rds-loader`** — Loading spinner.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `size` | string | | Spinner size |
| `color` | string | | Spinner color |
| `type` | string | | Spinner style variant |
| `text` | string | | Loading text label |

### Videos

**`video-caption-apollo`** — Video with caption/subtitle display.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `videoSource` | string | ✓ | Video URL |
| `caption` | string | | Caption/subtitle text |
| `captionPosition` | string | | Caption placement |

**`video-modal-atlas`** — Video in modal dialog.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `videoUrl` | string | ✓ | Video URL |
| `posterImage` | string | | Thumbnail image |
| `title` | string | | Modal title |

### Lists

**`list-timeline`** — Timeline/vertical list display.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `items` | array | ✓ | Array of timeline event objects |
| `connected` | boolean | | Show connecting lines between items |
| `direction` | string | | `vertical` or `horizontal` |

### Utilities

**`breadcrumb-apollo`** — Breadcrumb navigation.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `items` | array | ✓ | Array of `{ label, href }` crumb objects |
| `separator` | string | | Separator character (default: `/`) |
| `linkFormat` | string | | Link rendering format |

**`tab-switcher-apollo`** — Tab navigation component.

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `tabs` | array | ✓ | Array of tab definitions |
| `activeTab` | string | | Currently active tab ID |
| `variant` | string | | Visual variant |

---

## 4. Composition Patterns

### Standard Landing Page Layout

A typical RDS landing page follows this structure:

```vue
<template>
  <div>
    <!-- Hero -->
    <HeroStandardApollo
      :bg-image-source="homeData.hero.bgImageSource"
      :display-gradient="true"
      :full-width="true"
    >
      <h1>{{ homeData.hero.title }}</h1>
      <p>{{ homeData.hero.subtitle }}</p>
    </HeroStandardApollo>

    <!-- Intro / Content Section -->
    <SectionApollo :title="homeData.intro.title">
      <div v-html="homeData.intro.body" />
    </SectionApollo>

    <!-- Accordion / FAQ -->
    <OverlapAccordionAtlas
      :items="homeData.accordion"
      :display-label="homeData.accordionLabel"
    />

    <!-- Testimonial -->
    <SectionTestimonialFalcon
      :testimonials="homeData.testimonials"
      :title="homeData.testimonialTitle"
    />

    <!-- Cards Grid -->
    <SectionCardApollo
      :cards="homeData.cards"
      :title="homeData.cardsTitle"
      :columns="3"
    />

    <!-- Footer -->
    <FooterStandard />
  </div>
</template>

<script setup>
import homeData from '~/assets/content/home.json'
</script>
```

### MLB Reference Implementation Pattern

The production MLB landing page uses this proven component stack:

```
Page: pages/index.vue
├── HeroStandardApollo          ← hero with bg image + gradient
│   └── <h1> + subtitle
├── IntroTextComponent           ← custom component (no RDS equivalent)
├── OverlapAccordionAtlas        ← 4 accordion items from home.json
│   └── analytics tracking on collapse events
├── SectionApollo               ← CTA section with narrative text
│   └── <NuxtLink> to /requestinfo
├── SectionTestimonialFalcon    ← student success story
└── FooterStandard              ← standard footer

Data: assets/content/home.json
Analytics: analyticsComposable.trackEvent() on all interactions
```

### Form Page Pattern

```vue
<template>
  <div>
    <HeroStandardApollo :bg-image-source="formHero.image">
      <h1>{{ formHero.title }}</h1>
    </HeroStandardApollo>

    <SectionApollo title="Request Information">
      <form @submit.prevent="handleSubmit">
        <TypeinputText
          v-model="form.firstName"
          placeholder="First Name"
        />
        <TypeinputText
          v-model="form.lastName"
          placeholder="Last Name"
        />
        <TypeinputText
          v-model="form.email"
          placeholder="Email Address"
        />
        <PhoneInputApollo
          v-model="form.phone"
          country-code="US"
        />
        <DropdownApollo
          v-model="form.program"
          :options="programOptions"
          placeholder="Select a Program"
        />
        <FormCheckbox
          v-model="form.consent"
          label="I agree to the terms and conditions"
        />
        <button type="submit" class="btn btn-primary">Submit</button>
      </form>
    </SectionApollo>

    <FooterStandard />
  </div>
</template>

<script setup>
import formData from '~/assets/content/requestinfo.json'

const form = reactive({
  firstName: '',
  lastName: '',
  email: '',
  phone: '',
  program: null,
  consent: false,
})

const formHero = formData.hero
const programOptions = formData.programs
</script>
```

### Card Grid Pattern

```vue
<template>
  <SectionApollo :title="sectionTitle">
    <div class="row">
      <div
        v-for="card in cards"
        :key="card.id"
        class="col-md-4 mb-4"
      >
        <CardImageArticle
          :image-source="card.image"
          :title="card.title"
          :description="card.description"
        />
      </div>
    </div>
  </SectionApollo>
</template>
```

### Parallax + Stats Pattern

```vue
<template>
  <SectionParallaxApollo
    :image-source="parallaxBg"
    :title="statsTitle"
  >
    <div class="row text-center">
      <div v-for="stat in stats" :key="stat.label" class="col-md-3">
        <h2 class="display-4 fw-bold">{{ stat.value }}</h2>
        <p>{{ stat.label }}</p>
      </div>
    </div>
  </SectionParallaxApollo>
</template>
```

---

## 5. Content JSON Pattern

All page content is driven by JSON files in `assets/content/`. This separates content from presentation and enables non-developer content updates.

### Convention

```
assets/
└── content/
    ├── home.json           ← Homepage data
    ├── requestinfo.json    ← Form page data
    ├── programs.json       ← Program listings
    └── shared.json         ← Shared content (nav, footer)
```

### JSON Structure

Each JSON file maps directly to the components on its page:

```json
{
  "hero": {
    "title": "Transform Your Future",
    "subtitle": "World-class online education for working professionals",
    "bgImageSource": "images/hero-bg.jpg",
    "displayGradient": true
  },
  "intro": {
    "title": "Why Choose Our Programs?",
    "body": "<p>HTML content goes here with <strong>formatting</strong> preserved.</p>"
  },
  "accordion": [
    {
      "title": "Accessible online learning",
      "body": "<p>Our platforms are designed for flexibility...</p>",
      "accordionId": "acc-1",
      "image": "images/acc1.png"
    },
    {
      "title": "Same faculty, same degree",
      "body": "<p>Online students learn from the same faculty...</p>",
      "accordionId": "acc-2",
      "image": "images/acc2.jpeg"
    }
  ],
  "testimonials": [
    {
      "quote": "This program changed my career trajectory.",
      "author": "Jane Doe",
      "authorTitle": "Class of 2024",
      "image": "images/testimonial-jane.jpg"
    }
  ],
  "cards": [
    {
      "id": "card-1",
      "title": "Business Administration",
      "description": "Build leadership skills...",
      "image": "images/card-business.jpg",
      "link": "/programs/business"
    }
  ],
  "footer": {}
}
```

### Binding JSON to Components

```vue
<script setup>
import homeData from '~/assets/content/home.json'

// Direct prop spreading for simple components
const heroProps = homeData.hero
const accordionItems = homeData.accordion
const testimonials = homeData.testimonials
</script>

<template>
  <HeroStandardApollo v-bind="heroProps">
    <h1>{{ heroProps.title }}</h1>
  </HeroStandardApollo>

  <OverlapAccordionAtlas :items="accordionItems" />

  <SectionTestimonialFalcon :testimonials="testimonials" />
</template>
```

### Key Rules

1. **All user-facing text lives in JSON**, not in `.vue` templates
2. **Images** are referenced by relative path from `public/` directory (e.g., `"images/hero.jpg"`)
3. **HTML in body fields** is rendered with `v-html` — sanitize if content is user-generated
4. **Each JSON key** corresponds to a page section and maps 1:1 to a component
5. **Shared data** (navigation, footer, site-wide text) goes in `shared.json`

---

## 6. Fallback Rules

When the 94 RDS components don't cover a specific visual requirement:

### Decision Order

1. **First: Find the closest RDS component** — an approximate match using an RDS component is always preferred over custom HTML. Customize with slots, CSS overrides, and creative prop usage.

2. **Second: Compose from multiple RDS components** — combine two or more RDS components to achieve the desired layout (e.g., `SectionApollo` wrapping a custom grid of `CardIcon` components).

3. **Third: Create a minimal custom component** in the `components/` directory:

```vue
<!-- components/IntroTextComponent.vue -->
<!--
  Custom component: No direct RDS equivalent exists for an intro section
  with icon + two-column text layout. Uses Bootstrap 5 grid and RDS
  theme CSS variables for visual consistency.
-->
<template>
  <section class="intro-text py-5">
    <div class="container">
      <div class="row align-items-center">
        <div class="col-md-4 text-center">
          <img :src="icon" :alt="iconAlt" class="intro-icon" />
        </div>
        <div class="col-md-8">
          <h2>{{ title }}</h2>
          <div v-html="body" />
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
defineProps({
  title: String,
  body: String,
  icon: String,
  iconAlt: { type: String, default: '' },
})
</script>

<style scoped>
.intro-text {
  background-color: var(--rds-bg-primary);
  color: var(--rds-text-primary);
}
.intro-icon {
  max-width: 120px;
}
</style>
```

### Custom Component Requirements

- **Must use** Bootstrap 5 grid system (`container`, `row`, `col-*`)
- **Must use** RDS theme CSS variables (e.g., `var(--rds-bg-primary)`, `var(--rds-text-primary)`)
- **Must include** a comment block explaining why no RDS equivalent exists
- **Must follow** the same prop-driven, JSON-content pattern as RDS components
- **Must be placed** in the `components/` directory (auto-imported by Nuxt)

---

## 7. Import & Configuration Notes

### Auto-Import (No Manual Imports Needed)

RDS components are automatically registered via `nuxt.config.ts`:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  components: [
    {
      path: "~/node_modules/@rds-vue-ui/",
      ignore: ["**/index.ts", "**/index.js", "**/node_modules"],
    }
  ],
  build: {
    transpile: [
      'bootstrap',
      '@rds-vue-ui/',
    ]
  },
  css: [
    "@rds-vue-ui/rds-theme-base/style/rds-theme-base.scss",
    "~/assets/scss/styles.scss"
  ],
})
```

**This means:**
- ❌ Do NOT write `import { HeroStandardApollo } from '@rds-vue-ui/hero-standard-apollo'`
- ✅ Just use `<HeroStandardApollo>` directly in any template
- ✅ Use **PascalCase** component names in templates (e.g., `<HeroStandardApollo>`, `<SectionApollo>`)
- ✅ Components are resolved automatically from `~/node_modules/@rds-vue-ui/`

### Installing New Components

```bash
yarn add --registry=https://npm.edpl.us @rds-vue-ui/{package-name}
```

After installation, the component is immediately available in all templates — no config changes needed.

### Theme Setup

The base theme **must** be included in `nuxt.config.ts` CSS:

```typescript
css: [
  "@rds-vue-ui/rds-theme-base/style/rds-theme-base.scss",
  // Additional theme overrides:
  // "@rds-vue-ui/rds-theme-apollo/style/rds-theme-apollo.scss",
  // "@rds-vue-ui/rds-theme-atlas/style/rds-theme-atlas.scss",
]
```

Available themes: `rds-theme-base`, `rds-theme-apollo`, `rds-theme-atlas`, `rds-theme-airuniversity`, `rds-theme-army`, `rds-theme-dsl`, `rds-theme-starbucks`.

### Analytics Composable

The `analytics-gs-composable` is not a visual component — it's a Vue composable for Google Analytics:

```vue
<script setup>
import { useAnalytics } from '@rds-vue-ui/analytics-gs-composable'

const analytics = useAnalytics()

function onAccordionToggle(item) {
  analytics.trackEvent('accordion', 'toggle', item.title)
}
</script>
```

> **Note:** The analytics composable is the one exception to the "no manual imports" rule — composables must be imported explicitly.

---

## 8. Available Themes Reference

| Theme Package | Description | Use Case |
|---------------|-------------|----------|
| `rds-theme-base` | Base foundation styles (required) | Always include as the first CSS entry |
| `rds-theme-apollo` | Apollo design variant | Default landing page theme |
| `rds-theme-atlas` | Atlas design variant | Alternative modern theme |
| `rds-theme-airuniversity` | Air University branded | Military education sites |
| `rds-theme-army` | U.S. Army branded | Army-affiliated sites |
| `rds-theme-dsl` | DSL variant | Digital learning platforms |
| `rds-theme-starbucks` | Starbucks branded | Starbucks partnership sites |

---

## 9. Component Selection Examples

### Example 1: "I need a page with a big hero image, some FAQ questions, and a footer"

**Selection:**
1. `hero-standard-apollo` — Full-width hero with background image
2. `overlap-accordion-atlas` — FAQ accordion
3. `footer-standard` — Standard footer

### Example 2: "Show degree programs in a card layout with images"

**Selection:**
1. `section-card-apollo` — Wrapping section for the grid
2. `card-image-article` — Individual cards with image + title + description
3. Or `card-degree-text` if text-focused without images

### Example 3: "A form page to collect user information"

**Selection:**
1. `hero-standard-apollo` — Page hero
2. `section-apollo` — Form container section
3. `typeinput-text` — Name, email fields
4. `phone-input-apollo` — Phone field
5. `dropdown-apollo` — Program selection
6. `form-checkbox` — Consent checkbox
7. `footer-standard` — Page footer

### Example 4: "A page with parallax scrolling background and statistics"

**Selection:**
1. `section-parallax-apollo` — Parallax background section
2. Custom stats layout inside parallax using Bootstrap grid
3. Or `ranking-carousel-apollo` if stats are ranking-style

### Example 5: "Navigation that stays visible as user scrolls"

**Selection:**
1. `navbar-sticky-apollo` — Sticky nav (scrolls with page, sticks at top)
2. Or `navbar-fixed-atlas` — Fixed nav (always at top of viewport)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chandima) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
> Source: [tomevault-io/skills-registry](https://github.com/tomevault-io/skills-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->
