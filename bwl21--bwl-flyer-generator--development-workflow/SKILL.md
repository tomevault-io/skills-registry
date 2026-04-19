---
name: development-workflow
description: Apply when setting up the development environment, running dev server, building, testing, or deploying the extension. Covers npm commands, CORS configuration, debugging, and deployment to ChurchTools. Use when this capability is needed.
metadata:
  author: bwl21
---

# Development Workflow

## Environment Setup

1. Copy `.env-example` to `.env`
2. Configure ChurchTools URL and extension key:
   ```
   VITE_KEY=your-extension-key
   VITE_CHURCHTOOLS_URL=https://your-instance.church.tools
   ```

## Development Commands

```bash
npm run dev           # Start dev server with hot-reload (port 5173)
npm run build         # Production build
npm run preview       # Preview production build
npm run deploy        # Build and package for ChurchTools
npm run prettier:write # Format code
```

## CORS Configuration

For local development, configure CORS in ChurchTools:
- Admin > System Settings > Integrations > API > Cross-Origin Resource Sharing
- Add `http://localhost:5173`

### Safari Cookie Issues

Safari has stricter cookie handling. Solutions:
1. Use Vite proxy so API calls go through local server
2. Run dev server with HTTPS using [mkcert](https://github.com/FiloSottile/mkcert)

## Testing Checklist

1. Start dev server: `npm run dev`
2. Check browser console for errors
3. Test responsive design
4. Verify ChurchTools integration
5. Build test: `npm run build`
6. Deploy test: `npm run deploy`

## Deployment Process

1. Format code: `npm run prettier:write`
2. Build: `npm run build`
3. Package: `npm run deploy`
4. Upload `.zip` to ChurchTools Admin > Extensions
5. Configure extension settings
6. Test in production

## Debugging

Browser console tests:
```javascript
// Test API connection
churchtoolsClient.get('/whoami').then(console.log).catch(console.error)

// Check extension context
console.log('Extension context:', {
  url: window.location.href,
  parent: window.parent !== window,
  churchtoolsClient: !!window.churchtoolsClient
})
```

## Build Output

Check build output:
```bash
ls -la dist/
ls -lh dist/assets/
```

## Extension Context

- Extensions run in iframe context
- Use `window.parent.postMessage` for communication with ChurchTools
- Access ChurchTools API via `churchtoolsClient`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bwl21) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
