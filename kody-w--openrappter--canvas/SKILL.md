---
name: canvas
description: Generate and manipulate images using the HTML5 Canvas API through a headless browser or node-canvas. Use when this capability is needed.
metadata:
  author: kody-w
---

# Canvas

Create and manipulate images programmatically using canvas APIs.

## Node.js (node-canvas)

```javascript
const { createCanvas } = require('canvas');
const fs = require('fs');

const canvas = createCanvas(800, 600);
const ctx = canvas.getContext('2d');

ctx.fillStyle = '#1a1a2e';
ctx.fillRect(0, 0, 800, 600);
ctx.fillStyle = '#e94560';
ctx.font = 'bold 48px sans-serif';
ctx.fillText('Hello Canvas', 200, 300);

fs.writeFileSync('/tmp/canvas.png', canvas.toBuffer('image/png'));
```

## Use Cases

- Generate social media images
- Create charts and graphs
- Process and annotate screenshots
- Build thumbnail generators
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
