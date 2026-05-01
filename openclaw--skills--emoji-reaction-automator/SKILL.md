---
name: emoji-reaction-automator
description: Suggests emoji reactions for text messages based on sentiment analysis (positive, negative, funny, neutral). Use to increase social engagement and human-likeness in conversations. Use when this capability is needed.
metadata:
  author: openclaw
---

## Usage

```javascript
const { suggestReaction } = require('./index.js');

const text = "This is awesome! Great job.";
const suggestion = suggestReaction(text);
// Returns: { category: "positive", emoji: "👍", confidence: 0.9 }
```

## Supported Categories

- **Positive:** 👍, ❤️, 🙌, ✅
- **Negative:** 👎, 💔, ❌, ⚠️
- **Funny:** 😂, 🤣, 💀
- **Curious:** 🤔, 🧐, ❓
- **Excited:** 🎉, 🚀, 🔥
- **Neutral:** 👀, 🆗

## Notes

This is a lightweight rule-based sentiment mapper designed for quick reactions without heavy ML dependencies.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
