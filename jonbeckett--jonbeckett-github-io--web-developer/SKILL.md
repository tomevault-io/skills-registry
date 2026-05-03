---
name: jekyll-technical-setup
description: Jekyll configuration, local development commands, GitHub Pages deployment, theme setup, and technical considerations for jonbeckett.com site Use when this capability is needed.
metadata:
  author: jonbeckett
---

# Jekyll Configuration and Technical Setup

This skill covers Jekyll configuration, local development, deployment, and technical considerations specific to the jonbeckett.com site.

## Tech Stack Overview

- **Platform**: Jekyll static site generator
- **Theme**: Minimal Mistakes (remote theme)
- **Hosting**: GitHub Pages
- **Deployment**: Automated via GitHub Actions

## Local Development Commands

### Essential Commands
```bash
# Install dependencies
bundle install

# Serve locally with live reload
bundle exec jekyll serve

# Serve with drafts (for preview)
bundle exec jekyll serve --drafts

# Build site for production
bundle exec jekyll build

# Clean generated files
bundle exec jekyll clean
```

### Development Workflow
1. Make changes to posts or configuration
2. Test locally with `bundle exec jekyll serve`
3. Review changes at `http://localhost:4000`
4. Commit and push to trigger automatic deployment

## Jekyll Configuration Notes

### Layout Standards
- **All blog posts**: Use `layout: single`
- **Pages**: Use appropriate layout from Minimal Mistakes theme
- **Site-wide settings**: Configured in `_config.yml`

### Key Features Enabled
- Breadcrumbs: Enabled site-wide for navigation
- Comments: Currently disabled (can enable Giscus if needed)
- Analytics: Google Analytics configured
- Search: Built-in site search functionality

## Category and Tag Archives (CRITICAL)

**IMPORTANT**: This site uses GitHub Pages, which does NOT support the `jekyll-archives` plugin.

### Correct Configuration for GitHub Pages
```yaml
# CORRECT - works with GitHub Pages
category_archive:
  type: liquid
  path: /categories/
tag_archive:
  type: liquid  
  path: /tags/
```

### What NOT to Do
```yaml
# INCORRECT - will break site on GitHub Pages
category_archive:
  type: jekyll-archives
```

### How Archives Work
- The `liquid` method creates anchor links like `/categories/#productivity` and `/tags/#gtd`
- Category and tag pages use anchor links within the same page, not separate pages
- All categories and tags are displayed on single pages with JavaScript navigation

## File Organization Structure

```
_posts/
  └── YYYY/
      ├── YYYY-MM-DD-post-slug.md
      └── YYYY-MM-DD-another-post.md
_pages/
  ├── about.md
  ├── categories.md
  ├── posts.md
  └── tags.md
_data/
  └── navigation.yml
_includes/
  └── head/
      └── custom.html
assets/
  ├── css/
  │   └── main.scss
  └── images/
```

## Deployment Process

1. **Local Development**: Test changes locally
2. **Git Push**: Push to main branch
3. **GitHub Actions**: Automatic build and deployment
4. **GitHub Pages**: Site updated at jonbeckett.github.io

## Common Technical Issues

### Archive Configuration
- Never change archive type from `liquid` to `jekyll-archives`
- This will break all category and tag links site-wide
- GitHub Pages has limited plugin support

### Build Failures
- Check for YAML syntax errors in front matter
- Ensure all required front matter fields are present
- Verify Markdown syntax is correct
- Check for broken image URLs

### Performance Optimization
- Images are automatically optimized via Unsplash URL parameters
- CSS and JS are minified in production builds
- Site uses CDN for theme assets

## Troubleshooting Checklist

### Pre-Deployment Validation
1. ✅ YAML front matter is valid and complete
2. ✅ File naming convention is followed exactly
3. ✅ Images load correctly from Unsplash
4. ✅ Local build succeeds without errors
5. ✅ All internal links are valid
6. ✅ British English spelling is used throughout

### Common Issues and Solutions

#### Build Fails Locally
- **YAML syntax errors**: Check front matter indentation and quotes
- **Missing front matter fields**: Ensure title, layout, date are present
- **Broken image URLs**: Verify Unsplash photo IDs and test URLs
- **Markdown syntax errors**: Check for unmatched code blocks or headers

#### Site Doesn't Update After Deploy
- **GitHub Pages build failure**: Check Actions tab for build errors
- **Cache issues**: Wait 5-10 minutes or hard refresh browser
- **Archive configuration**: Verify `type: liquid` in _config.yml

#### Images Not Displaying
- **Photo ID format**: Must be `photo-[timestamp]-[alphanumeric]`
- **Premium images**: Find alternative free Unsplash images
- **URL parameters**: Ensure correct format with width, height, quality
- **Attribution missing**: Always include photographer credit

#### Categories/Tags Not Working
- **Archive type**: Must use `liquid`, never `jekyll-archives` on GitHub Pages
- **Case sensitivity**: Use lowercase with hyphens consistently
- **Category pages**: Links go to `/categories/#category-name` anchors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonbeckett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
