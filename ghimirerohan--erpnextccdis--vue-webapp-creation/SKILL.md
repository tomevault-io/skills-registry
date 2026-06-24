---
name: vue-webapp-creation
description: Create new Vue web applications within the custom_erp Frappe app. Use when the user wants to create a new Vue app, add a new frontend app, create a new PWA, or asks about adding apps like qrpay, pay-dashboard. Covers directory structure, routing, Vite config, Frappe www files, PWA manifest, and deployment. Use when this capability is needed.
metadata:
  author: ghimirerohan
---

# Vue WebApp Creation in Custom_ERP

This skill guides you through creating a new Vue web application within the custom_erp Frappe app, from initial setup to production deployment.

## Quick Reference

For a new app called `my-app` at route `/my-app`, you need:

| File/Directory | Purpose |
|----------------|---------|
| `my-app/` | Source directory with `index.html` and `src/` |
| `my-app/src/main.js` | Vue app initialization |
| `my-app/src/App.vue` | Root component with PWA setup |
| `my-app/src/router.js` | Vue Router with auth guards |
| `my-app/src/MyApp.vue` | Main page component |
| `custom_erp/www/my-app.html` | Frappe production entry (hyphenated) |
| `custom_erp/www/my_app.py` | Frappe context (underscored) |
| `public/manifest-my-app.json` | PWA manifest |
| `public/icons/my-app/icon-512x512.png` | App icon |
| `vite.config.js` | Add to apps array |
| `build-apps.js` | Add to apps array + theme config |

## Naming Convention

**CRITICAL**: Follow this naming pattern exactly:

| Location | Format | Example |
|----------|--------|---------|
| Directory & URL | hyphenated | `my-app`, `/my-app` |
| www/*.html | hyphenated | `my-app.html` |
| www/*.py | underscored | `my_app.py` |
| vite.config.js | hyphenated | `'my-app'` |
| build-apps.js | hyphenated | `'my-app'` |
| manifest | hyphenated | `manifest-my-app.json` |
| Router base | hyphenated | `/my-app` |

---

## Step-by-Step Creation Process

### Step 1: Create Directory Structure

```bash
cd frappe-bench/apps/custom_erp
mkdir -p my-app/src
```

### Step 2: Create Source Files

Create all files in this order. See [templates.md](templates.md) for complete file contents.

1. `my-app/index.html` - Dev entry point
2. `my-app/src/main.js` - Vue initialization
3. `my-app/src/App.vue` - Root component
4. `my-app/src/router.js` - Router with auth
5. `my-app/src/MyApp.vue` - Main component

### Step 3: Update Build Configurations

**vite.config.js** - Add to apps array:
```javascript
const apps = [
  'qrpay',
  'my-app',  // ADD HERE
  // ... other apps
]
```

**build-apps.js** - Add to apps array AND appThemes:
```javascript
const apps = [
  'qrpay',
  'my-app',  // ADD HERE
  // ...
]

const appThemes = {
  // ...
  'my-app': { 
    theme: '#3b82f6',      // Primary color (hex)
    bg: '#ffffff',          // Background color
    name: 'My App',         // Display name
    desc: 'App description' // PWA description
  },
}
```

### Step 4: Create Frappe WWW Files

**custom_erp/www/my-app.html** (placeholder - build will update):
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" href="/assets/custom_erp/frontend/my-app/favicon.png" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
    <title>My App - Description</title>
    <meta name="theme-color" content="#3b82f6" />
    <meta name="mobile-web-app-capable" content="yes" />
    <link rel="manifest" href="/api/method/custom_erp.api.pwa.get_manifest?app_name=my-app" />
  </head>
  <body>
    <div id="app"></div>
    <script>
        {% for key in boot %}
        window["{{ key }}"] = {{ boot[key] | tojson }};
        {% endfor %}
    </script>
  </body>
</html>
```

**custom_erp/www/my_app.py** (NOTE: underscored filename):
```python
import frappe

def get_context(context):
    """Context for my-app"""
    context.no_cache = 1
    return context
```

### Step 5: Create PWA Manifest

**public/manifest-my-app.json**:
```json
{
  "id": "/my-app/",
  "name": "My App",
  "short_name": "MyApp",
  "description": "App description",
  "start_url": "/my-app/",
  "scope": "/my-app/",
  "display": "standalone",
  "orientation": "portrait-primary",
  "background_color": "#ffffff",
  "theme_color": "#3b82f6",
  "icons": [
    {
      "src": "/assets/custom_erp/icons/my-app/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ],
  "categories": ["business", "finance"],
  "prefer_related_applications": false
}
```

### Step 6: Add App Icon

```bash
mkdir -p public/icons/my-app
cp public/icons/qrpay/icon-512x512.png public/icons/my-app/
```

Or create a custom 512x512 PNG icon.

---

## Testing & Deployment

### Local Development
```bash
cd frappe-bench/apps/custom_erp
yarn dev
# Access at http://localhost:8080/my-app
```

### Build for Production
```bash
cd frappe-bench/apps/custom_erp
node build-apps.js
```

### Commit & Push
```bash
git add -A
git commit -m "feat: add my-app Vue web application"
git push origin main
```

### Deploy to Server
```bash
# On production server:
cd ~/frappe-bench/apps/custom_erp
git pull origin main
node build-apps.js
cd ~/frappe-bench
bench build --app custom_erp
bench restart
```

---

## Checklist

Copy this checklist when creating a new app:

```
New Vue App: [APP_NAME]

Directory & Source Files:
- [ ] Created [app-name]/ directory
- [ ] Created [app-name]/index.html
- [ ] Created [app-name]/src/main.js
- [ ] Created [app-name]/src/App.vue
- [ ] Created [app-name]/src/router.js (base path: /[app-name])
- [ ] Created [app-name]/src/[AppName].vue

Build Configuration:
- [ ] Added to vite.config.js apps array
- [ ] Added to build-apps.js apps array
- [ ] Added theme config to build-apps.js appThemes

Frappe Files:
- [ ] Created custom_erp/www/[app-name].html
- [ ] Created custom_erp/www/[app_name].py (underscored!)

PWA Assets:
- [ ] Created public/manifest-[app-name].json
- [ ] Added icon to public/icons/[app-name]/icon-512x512.png

Testing:
- [ ] Tested locally with yarn dev
- [ ] Built with node build-apps.js
- [ ] Verified at /[app-name] route

Deployment:
- [ ] Committed and pushed
- [ ] Pulled on server
- [ ] Ran node build-apps.js on server
- [ ] Ran bench build --app custom_erp
- [ ] Ran bench restart
```

---

## Common Issues & Solutions

### 404 Errors for Assets
**Cause**: Build output doesn't exist or wrong paths
**Fix**: Run `node build-apps.js` - it generates correct asset hashes

### PWA Manifest 404
**Cause**: App not in build-apps.js or manifest not generated
**Fix**: Add to build-apps.js apps array and rebuild

### Login Redirect Loop
**Cause**: Wrong router base path
**Fix**: Ensure `createWebHistory("/my-app")` matches your URL

### Python File Not Found
**Cause**: Wrong filename format
**Fix**: WWW Python files use underscores: `my_app.py` (not `my-app.py`)

---

## Additional Resources

- See [templates.md](templates.md) for complete file templates
- See [examples.md](examples.md) for real app examples
- Reference existing apps: `qrpay/`, `pay-dashboard/`, `qrpay-horlicks/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghimirerohan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
