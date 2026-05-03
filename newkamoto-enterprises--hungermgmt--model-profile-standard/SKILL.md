---
name: hunger-management-model-profile-standard
description: Defines the photo style, required assets, profile data structure, and website organization for adding new models to the HUNGER MANAGEMENT agency website Use when this capability is needed.
metadata:
  author: newkamoto-enterprises
---

# HUNGER MANAGEMENT Model Profile Standard

This skill documents the complete standard for creating and managing model profiles on the HUNGER MANAGEMENT modeling agency website.

---

## 1. Photo Style Guide

### Core Aesthetic

All photos follow a **clean, minimalist, high-fashion casting style** designed for maximum visual impact with the website's brutalist grayscale-to-color hover effect.

| Attribute | Standard |
|-----------|----------|
| **Background** | Pure white (#FFFFFF) or off-white seamless |
| **Lighting** | Even, flat, professional studio lighting |
| **Expression** | Neutral, serious, unemotional (casting standard) |
| **Clothing** | Solid black (t-shirt, jeans, simple top/pants) |
| **Makeup** | Minimal to none — natural skin visible |
| **Hair** | Natural styling, not heavily styled |
| **Retouching** | Minimal — skin texture, freckles, and features visible |

### Wardrobe Requirements

- **Top**: Plain black crew-neck t-shirt (unisex standard)
- **Bottom**: Black jeans or black trousers
- **Footwear**: Black leather shoes or sandals (visible in full-body shots)
- **No logos, patterns, or accessories**

---

## 2. Required Photos Per Model

Each model profile requires **4 photo types** following strict naming conventions:

### Photo Types

| Type | Naming Convention | Composition | Purpose |
|------|-------------------|-------------|---------|
| **Thumbnail** | `{name}_thumb.png` | Tight headshot, face centered, shoulders visible | Grid display card |
| **Main/Hero** | `{name}_main.png` OR `{uuid}.png` | Close-up portrait, face and upper chest | Profile hero image |
| **Full-Body** | `{name}_fullbody_{n}.png` | Head-to-toe standing pose, centered | Body proportions showcase |
| **Side Profile** | `{name}_side_{n}.png` | 3/4 turn OR true profile angle | Facial structure detail |

### Photo Specifications

| Spec | Requirement |
|------|-------------|
| **Format** | PNG (transparent-safe, high quality) |
| **Resolution** | Minimum 800×1000px, recommended 2000×2500px+ |
| **Aspect Ratio** | Portrait orientation (3:4 for grid, flexible for gallery) |
| **File Location** | `models photos/{filename}` |

### Naming Examples

```
alina_thumb.png        # Thumbnail for grid
alina_main.png         # Hero portrait (or use UUID)
alina_fullbody_1.png   # Full-body shot 1
alina_side_1.png       # Side profile shot
```

> [!NOTE]
> The `{n}` suffix (e.g., `_1`, `_2`) allows for multiple variations of the same shot type.

---

## 3. Profile Data Structure

Each model is defined as a JavaScript object in `script.js`:

```javascript
{
    id: "alina",                          // Unique lowercase identifier (URL slug)
    name: "ALINA",                        // Display name (UPPERCASE)
    mainImage: "models photos/alina_main.png",  // Hero image path
    thumbnail: "models photos/alina_thumb.png", // Grid thumbnail path
    images: [                             // Gallery images array
        "models photos/alina_fullbody_1.png",
        "models photos/alina_side_1.png"
    ],
    stats: {                              // Body measurements object
        height: "178cm",
        bust: "80cm",      // Female models
        // OR chest: "96cm", // Male models
        waist: "60cm",
        hips: "88cm",
        shoes: "39"
    },
    skills: "Runway, Editorial, Dance"    // Comma-separated skill tags
}
```

### Stats Fields by Gender

| Female Models | Male Models |
|---------------|-------------|
| `height` | `height` |
| `bust` | `chest` |
| `waist` | `waist` |
| `hips` | `hips` |
| `shoes` | `shoes` |

### Skills Taxonomy

Common skill tags used:
- **Categories**: Runway, Editorial, Commercial, High Fashion, Catalog
- **Styles**: Streetwear, Avant-Garde, Classic, Suit, Swim
- **Talents**: Dance, Acting, Sports, Music, Performance, Art, Video, Skate

---

## 4. Website Organization

### Architecture

```
hungermgmt/
├── index.html          # Main HTML shell
├── style.css           # Brutalist styling
├── script.js           # Model data + routing logic
├── CNAME               # Domain configuration
└── models photos/      # All model images
    ├── {name}_thumb.png
    ├── {name}_main.png
    ├── {name}_fullbody_{n}.png
    └── {name}_side_{n}.png
```

### View System

| View | URL Pattern | Description |
|------|-------------|-------------|
| **Grid View** | `/` or `/#` | All model thumbnails in responsive grid |
| **Profile View** | `/#modelid` | Individual model's full profile |

### Design System

```css
/* Brutalist Core */
Font:           'Arial Narrow', bold
Background:     White (#fff)
Text:           Black (#000)
Borders:        1px solid black
Grid:           auto-fill, minmax(300px, 1fr)
Card Ratio:     3:4 aspect ratio

/* Interaction */
Default:        Grayscale filter (100%)
Hover:          Full color reveal
Transition:     0.3s ease filter
```

---

## 5. Adding a New Model

### Step-by-Step Process

1. **Prepare Photos**
   - [ ] Shoot photos following the Photo Style Guide above
   - [ ] Export as PNG to `models photos/` directory
   - [ ] Use correct naming convention: `{name}_thumb.png`, etc.

2. **Add Model Data**
   - [ ] Open `script.js`
   - [ ] Add new object to the `models` array (before the closing `]`)
   - [ ] Follow the data structure template:

```javascript
{
    id: "newmodel",               // lowercase, no spaces
    name: "NEWMODEL",             // UPPERCASE display name
    mainImage: "models photos/newmodel_main.png",
    thumbnail: "models photos/newmodel_thumb.png",
    images: [
        "models photos/newmodel_fullbody_1.png",
        "models photos/newmodel_side_1.png"
    ],
    stats: { height: "___cm", bust/chest: "___cm", waist: "___cm", hips: "___cm", shoes: "__" },
    skills: "Category1, Category2, Talent"
},
```

3. **Test Profile**
   - [ ] Open `index.html` in browser
   - [ ] Verify thumbnail appears in grid
   - [ ] Click to verify profile view loads
   - [ ] Check all images display correctly
   - [ ] Verify grayscale → color hover effect

4. **Deploy**
   - [ ] Commit changes: `git add . && git commit -m "Add model: NEWMODEL"`
   - [ ] Push to GitHub: `git push`
   - [ ] Verify live at domain (check CNAME)

---

## 6. Profile View Layout

The profile page uses a **split-panel layout**:

```
┌─────────────────────────────────────────────────────────┐
│                    HUNGER MANAGEMENT                     │
├──────────────────┬──────────────────────────────────────┤
│                  │                                      │
│  MODEL NAME      │  ┌────────────────────────────────┐  │
│                  │  │                                │  │
│  HEIGHT    XXXcm │  │         MAIN IMAGE             │  │
│  BUST      XXXcm │  │                                │  │
│  WAIST     XXXcm │  │                                │  │
│  HIPS      XXXcm │  └────────────────────────────────┘  │
│  SHOES     XX    │  ┌────────────────────────────────┐  │
│                  │  │       FULL-BODY IMAGE          │  │
│  SKILLS          │  │                                │  │
│  Tag1, Tag2      │  └────────────────────────────────┘  │
│                  │  ┌────────────────────────────────┐  │
│  [SHARE LINK]    │  │        SIDE PROFILE            │  │
│  [← BACK]        │  │                                │  │
│                  │  └────────────────────────────────┘  │
└──────────────────┴──────────────────────────────────────┘
```

- **Sidebar**: 300px fixed width, sticky on scroll
- **Gallery**: Fluid width, vertical scroll of images
- **Mobile**: Stacks to single column layout

---

## 7. Quality Checklist

Before adding any new model, verify:

### Photos
- [ ] All 4 photo types captured (thumb, main, fullbody, side)
- [ ] White/off-white background
- [ ] Black clothing only
- [ ] Neutral expression
- [ ] Minimal retouching
- [ ] PNG format, high resolution

### Data
- [ ] Unique `id` (no duplicates in array)
- [ ] Name in UPPERCASE
- [ ] All file paths correct and files exist
- [ ] Stats complete with correct measurement units (cm)
- [ ] Skills use existing taxonomy where possible

### Display
- [ ] Thumbnail looks good in grayscale
- [ ] Color reveal on hover is appealing
- [ ] All gallery images load in profile view
- [ ] Mobile layout works correctly

---

## 8. File Reference

Current models in the system:

| ID | Name | Status |
|----|------|--------|
| alina | ALINA | ✓ Complete |
| leonid | LEONID | ✓ Complete |
| wan | WAN | ✓ Complete |
| harper | HARPER | ✓ Complete |
| jacob | JACOB | ✓ Complete |
| dan | DAN | ✓ Complete |
| colborn | COLBORN | ✓ Complete |
| nadia | NADIA | ✓ Complete |
| arthur | ARTHUR | ✓ Complete |
| esmeralda | ESMERALDA | ✓ Complete |
| zoe | ZOE | ✓ Complete |
| geoffrey | GEOFFREY | ✓ Complete |

**Total Models**: 12

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newkamoto-enterprises) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
