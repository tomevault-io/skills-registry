---
name: metalsmith-component-builder
description: Build static websites using Metalsmith's component-based architecture. Triggers on phrases like "build me a website", "create a landing page", "help me make a site", or when working with Metalsmith components. Assumes user has VS Code, Node.js, Git, and is running Claude Code in an empty project directory. Use when this capability is needed.
metadata:
  author: wernerglinka
---

# Metalsmith Component Website Builder

Build professional static websites through conversation. The user describes what they want, Claude handles the implementation using Metalsmith's component-based architecture.

## Prerequisites (User Has Already Done This)

The blogpost at [link to blogpost] walked users through setup before they started Claude Code:

- VS Code installed
- Node.js 18+ installed
- Git installed and configured
- GitHub account created
- Netlify account created (connected to GitHub)
- This skill installed at `~/.claude/skills/metalsmith-component-builder-skill`

Do not re-verify these unless the user reports problems.

## Authoritative Resources

**https://metalsmith-components.com** is the authoritative source for component configuration and patterns. When uncertain about component options:

1. Fetch reference pages at `/references/sections/[component-name]/`
2. Fetch blog posts for conceptual understanding
3. Check `/blog/installing-metalsmith-components/` for installation patterns

Key documentation:
- `/section-anatomy/` - How components are structured
- `/yaml-to-html/` - The rendering process
- `/blog/building-pages-with-components/` - Page construction patterns

## Workflow Overview

1. **Project Setup** - Clone starter, install dependencies, start dev server
2. **Discovery Dialog** - Understand what the website needs
3. **Component Selection** - Identify required components, download and install
4. **Page Building** - Create pages iteratively with live preview
5. **Review & Refine** - User previews at localhost, provides feedback
6. **Publish** - Push to GitHub, Netlify auto-deploys

---

## Phase 1: Project Setup

When user says they want to build a website, immediately set up the project:

```bash
# Clone the micro starter into current directory
git clone https://github.com/wernerglinka/microStarter.git .

# Install dependencies
npm install

# Start development server
npm start
```

Tell the user:
> "I've set up your project. Open another terminal and the dev server is running at http://localhost:3000. You should see a basic starter page. Keep that running while we work."

Wait for confirmation before proceeding.

---

## Phase 2: Discovery Dialog

Have a conversation to understand what the user needs. Ask these questions naturally, not as a checklist:

**About the website**
- What is this website for? (business, portfolio, nonprofit, personal, event)
- Who will visit it?
- What should visitors do when they arrive?

**About the homepage**
- What's the first thing visitors should see?
- What's the main message or call to action?
- Do you have images or video in mind?

**About other pages**
- What pages beyond the homepage? (About, Services, Contact, Blog, Team, Pricing)
- Do you have content ready or do you need help writing it?

**About special features**
- Need a contact form?
- Will you write blog posts?
- Any special features? (search, image galleries, testimonials)

Summarize what you learned before proceeding:
> "So you need a photography portfolio with: a hero section showing your best work, an about page with your background, a gallery page, and a contact form. Does that sound right?"

---

## Phase 3: Component Selection

Based on discovery, determine which components are needed.

**Included in starter:**
- `text-only` - Text content with optional CTAs
- `header` - Site header with navigation
- `footer` - Site footer

**Available at metalsmith-components.com:**

| Category | Components |
|----------|-----------|
| Content | hero, banner, multi-media, columns, compound |
| Media | image-only, video-only, audio-only, image-grid, image-compare |
| Interactive | accordion, flip-cards, simple-slider, hero-slider |
| Lists | blurbs, manual-card, collection-list, logos-list, team-grid |
| Specialized | testimonial, pricing-table, stats, steps, timeline, maps, search |

**Download and install needed components:**

```bash
# Download component
curl -L https://metalsmith-components.com/downloads/sections/hero.zip -o hero.zip

# Extract
unzip hero.zip

# Run install script
cd hero
chmod +x install.sh
./install.sh

# Clean up
cd ..
rm -rf hero hero.zip
```

The install script automatically downloads and installs any missing dependencies. If hero requires the `icon` partial and it's not installed, the script fetches and installs it before completing. Nested dependencies are handled recursively.

After installing components, restart the dev server:
```bash
# In the terminal running npm start, press Ctrl+C then:
npm start
```

---

## Phase 4: Page Building

Create pages in `src/` with YAML frontmatter. Every page follows this structure:

```yaml
---
layout: pages/sections.njk
bodyClasses: page-name

navigation:
  navLabel: Page Name
  navIndex: 1

seo:
  title: Page Title
  description: Page description for search engines

sections:
  - sectionType: component-name
    # section configuration...
---
```

### First Section Padding

The first section on any page needs extra top padding to clear the fixed header. Add `first-section` to the classes:

```yaml
sections:
  - sectionType: hero
    classes: first-section
    # ... rest of config
```

This applies to whatever section type appears first on a page, not just heroes.

### Site Configuration

Update `lib/data/site.json` with the user's information:

```json
{
  "title": "Site Name",
  "description": "Site description",
  "url": "https://their-domain.com/",
  "locale": "en_US",
  "defaultImage": "/assets/images/social.png",
  "siteOwner": "Owner Name"
}
```

### Section Configuration Pattern

All sections support these common options:

```yaml
- sectionType: component-name
  containerTag: section
  containerFields:
    inContainer: true
    isAnimated: true
    isDark: false
    background:
      color: ""
      image: /assets/images/bg.jpg
      imageScreen: dark
    noMargin:
      top: false
      bottom: false
    noPadding:
      top: false
      bottom: false
  text:
    leadIn: Eyebrow text
    title: Main Title
    titleTag: h2
    subTitle: Subtitle
    prose: |-
      Markdown content here.
      Multiple paragraphs supported.
  ctas:
    - url: /path
      label: Button Text
      isButton: true
      buttonStyle: primary
```

### Build Pages Incrementally

1. Create or modify a page file
2. Tell user to refresh localhost:3000
3. Ask for feedback
4. Refine based on response
5. Move to next page

Example workflow:
> "I've created your homepage with a hero section and about section. Refresh your browser and let me know what you think. Should the hero image be darker? Is the headline right?"

---

## Phase 5: Review & Refine

After each significant change:

1. Remind user to check localhost:3000
2. Ask specific questions about what they see
3. Make adjustments based on feedback
4. Repeat until satisfied

Common refinements:
- Adjusting text content
- Changing section order
- Modifying background colors/images
- Adding or removing CTAs
- Tweaking spacing (noMargin, noPadding)

---

## Phase 6: Publish

When the user is happy with their site:

### Set up Git remote

```bash
# Remove link to starter repo
git remote remove origin
```

Ask user for their GitHub username and repository name, then:

```bash
git remote add origin https://github.com/USERNAME/REPO-NAME.git
git add .
git commit -m "Initial website"
git branch -M main
git push -u origin main
```

If they haven't created the repo yet, guide them:
> "Go to github.com, click the + icon, select 'New repository', name it 'my-website', keep it public, and don't initialize with any files. Then tell me the URL."

### Netlify deployment

If they connected Netlify to GitHub during setup (per blogpost), the site deploys automatically.

If not, guide them:
1. Go to app.netlify.com
2. Click "Add new site" → "Import an existing project"  
3. Click "Deploy with GitHub"
4. Select the repository
5. Build command: `npm run build`
6. Publish directory: `build`
7. Click "Deploy site"

Tell them:
> "Your site is deploying. In about a minute, Netlify will give you a URL like random-name-123.netlify.app. That's your live website."

---

## Making Future Changes

When user returns to make updates:

1. They open the project folder in VS Code
2. Start Claude Code: `claude`
3. Describe what they want to change
4. Claude modifies files
5. User previews at localhost:3000
6. When satisfied:

```bash
git add .
git commit -m "Description of changes"
git push
```

Netlify auto-deploys. Site updates in ~1 minute.

---

## Component Reference

For detailed configuration of each component, fetch from metalsmith-components.com:

```
https://metalsmith-components.com/references/sections/[component-name]/
```

### Data-Driven Components

Some components (blurbs, logos-list, team-grid, etc.) expect data from JSON files in `lib/data/` rather than inline in frontmatter.

**Blurbs example:**

Create `lib/data/blurbs.json`:
```json
{
  "services": [
    {
      "decoration": { "icon": "message-circle" },
      "text": {
        "title": "Service One",
        "prose": "Description here."
      }
    }
  ]
}
```

Reference in frontmatter:
```yaml
- sectionType: blurbs
  blurbs:
    source: services
    layout: inline
```

After creating new data files, restart the dev server.

### Quick Examples

**Hero with background image:**
```yaml
- sectionType: hero
  containerFields:
    background:
      image: /assets/images/hero-bg.jpg
      imageScreen: dark
  text:
    title: Welcome
    prose: Your introduction here.
  ctas:
    - url: /contact
      label: Get Started
      isButton: true
      buttonStyle: primary
  image:
    src: /assets/images/hero-image.jpg
    alt: Description
```

**Multi-media (text + image side by side):**
```yaml
- sectionType: multi-media
  text:
    title: About Us
    prose: Our story.
  media:
    type: image
    src: /assets/images/about.jpg
    alt: About image
  isReverse: true
```

**Image grid:**
```yaml
- sectionType: image-grid
  text:
    title: Our Work
  images:
    - src: /assets/images/work-1.jpg
      alt: Project 1
    - src: /assets/images/work-2.jpg
      alt: Project 2
    - src: /assets/images/work-3.jpg
      alt: Project 3
```

---

## Troubleshooting

**YAML errors**: Check indentation. Use 2 spaces, no tabs. YAML is sensitive to spacing.

**Section not rendering**: Verify `sectionType` matches the folder name in `lib/layouts/components/sections/`.

**Images not showing**: Paths must start with `/assets/`. Files go in `lib/assets/images/`.

**Styles missing after adding component**: Restart dev server (Ctrl+C, then `npm start`).

**Text invisible on dark background**: Use `isDark: true` in containerFields when using a dark background color. This switches text to light colors. Note: `imageScreen: dark` only adds an overlay on background images, not solid colors.

**New data file not picked up**: When creating new JSON files in `lib/data/`, restart the dev server manually after the file is written. The watcher may catch an incomplete file mid-write and error.

**Git push fails**: Check that the remote repository exists and user has access. Verify with `git remote -v`.

**Netlify build fails**: Check the deploy log in Netlify dashboard. Usually a missing dependency or incorrect build command.

---

## File Structure Reference

```
my-website/
├── src/                          # Content pages
│   ├── index.md                  # Homepage
│   └── [other-pages].md
├── lib/
│   ├── assets/                   # Static files
│   │   ├── images/
│   │   ├── styles/
│   │   └── scripts/
│   ├── data/
│   │   └── site.json             # Site configuration
│   └── layouts/
│       ├── components/
│       │   ├── sections/         # Section components
│       │   └── _partials/        # Shared partials
│       └── pages/
│           └── sections.njk      # Page template
├── metalsmith.js                 # Build configuration
└── package.json
```

---

## Learning More

- **metalsmith-components.com** - Component reference and documentation
- **glinka.co/blog** - Metalsmith Redux series with in-depth tutorials
- `references/learning-resources.md` - Full resource list

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wernerglinka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
