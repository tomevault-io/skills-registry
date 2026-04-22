---
name: ormus-illustrator
description: Generate AI illustrations and videos. Triggers when user wants images, videos, or animations created. Supports image-to-video and text-to-video generation. Use when this capability is needed.
metadata:
  author: hermeticormus
---

# Ormus Illustrator

Multi-provider AI illustration engine with an LLM-first philosophy.

> **"LLMs are the chill, reliable grown-up in the room."**

---

## The Core Philosophy

Image generators wake up each morning with a new personality. Even the best ones struggle with consistent personal style. Midjourney has taste, but also a temper.

**The solution:** Use Claude as a thinking partner *before* touching any image tool.

| Role | Responsibility |
|------|----------------|
| **Claude** | Structure, consistency, prompt refinement |
| **Image Tool** | Aesthetics, visual execution |
| **You** | Taste, curation, direction |

This creates stability in an inherently chaotic process.

---

## Phase 0: Think Before You Generate

**Before jumping into any provider, have a conversation with Claude about:**

### 1. Context Building
Ask yourself (or let Claude help you articulate):
- **What** am I creating? (illustration, icon, character, scene?)
- **Who** is it for? (children, developers, myself?)
- **Tone** — playful, serious, ethereal, scientific?
- **Colors** — specific palette or mood-driven?
- **References** — what existing work feels "right"?

### 2. Personal Design Scaffold (Optional but Powerful)
Write down how you think when designing:
- What do you look at first?
- How do you choose a direction?
- What do you avoid?
- How do you explore ideas?

Once Claude knows your logic, prompts feel surprisingly aligned.

### 3. Example Conversation Opener
```
I'm creating illustrations for [project].
Target audience: [who]
Feeling I want: [adjectives]
Colors I'm drawn to: [colors]
Things I want to avoid: [anti-patterns]
References that feel right: [links or descriptions]

Help me think through this before we generate anything.
```

---

## The Positive Loop

This is where the magic happens:

```
┌─────────────────────────────────────────────────────┐
│                                                      │
│   Claude      →     Prompt     →    Image Tool      │
│     ↑                                    │          │
│     │                                    ↓          │
│   Tell Claude    ←   Mood Board   ←   Output       │
│   "we evolved"                                      │
│                                                      │
└─────────────────────────────────────────────────────┘
```

1. **Chat with Claude** about what you're making
2. **Share references** from your mood board
3. **Let Claude build prompts** that match your logic
4. **Run them** in the appropriate provider
5. **Take good results** → add to mood board
6. **Tell Claude** "Look, we just evolved the style!"

After a few rounds, your style becomes surprisingly stable.

---

## Mood Board Workflow

Your mood board is your **visual anchor**.

**Collect things you love:**
- Colors and gradients
- Textures and patterns
- Photography/illustration styles
- Small visual cues that feel "right"

**Keep it clean.** A cluttered board = unclear direction.

**Storage suggestion:** `~/projects/[project]/mood-board/`

**Evolve it.** As you generate good outputs, add them back. The board grows with your style.

---

## When This Skill Activates

- User says "illustrate", "draw", "create an image", "generate a picture"
- User requests visual content for any project
- User needs graphics, artwork, or rendered images
- User mentions "midjourney" for artistic/hand-drawn styles
- User wants "pixel art", "animation", "sprite", or "animate"

---

## Available Providers

| Provider | Best For | Personality | Speed | Cost |
|----------|----------|-------------|-------|------|
| **Gemini** | Botanical, scientific | Precise, literal | Fast | Free (quota) |
| **Replicate** | Icons, vectors, **video** | Versatile, predictable | Medium | ~$0.003-0.07 |
| **Midjourney** | Artistic, hand-drawn, childlike | Has taste, has temper | Slow | Subscription |
| **PixelLab** | Pixel art, game animations | Consistent, game-focused | Fast | Credits-based |

---

## Provider 1: Gemini (Default)

```bash
cd ~/.claude/plugins/ormus-illustrator && python3 generate.py "PROMPT" --style STYLE --name FILENAME
```

**Style presets:** `botanical`, `scientific`, `alchemical`, `minimal`, `none`

**Auth:** `~/google-cloud-sdk/bin/gcloud auth application-default login`

---

## Provider 2: Replicate (Images & Video)

```bash
cd ~/.claude/plugins/ormus-illustrator/providers && python3 replicate.py "PROMPT" --model MODEL --name FILENAME
```

### Image Models

| Model | Best For |
|-------|----------|
| `flux-schnell` | Fast drafts (~$0.003) |
| `flux-pro` | High quality |
| `recraft-v3` | Icons, text, logos |
| `recraft-svg` | Clean SVG vectors |
| `ideogram-v2` | Perfect text in images |

**List image models:** `python3 replicate.py --list-models`

### Video Models (NEW)

| Model | Type | Best For |
|-------|------|----------|
| `svd` | Image→Video | Animate a still image (~$0.07) |
| `animate-diff` | Image→Video | Motion with prompt control |
| `i2vgen-xl` | Image→Video | High quality animation |
| `minimax` | Text→Video | Generate video from description |
| `hunyuan` | Text→Video | High quality text-to-video |
| `ltx-video` | Text→Video | Fast text-to-video |
| `liveportrait` | Animation | Animate faces/talking heads |

**List video models:** `python3 replicate.py --list-video-models`

### Video Generation Examples

**Image-to-Video (animate a still image):**
```bash
python3 replicate.py --video --model svd --image my-character.png --name character-animated
```

**Text-to-Video (generate from description):**
```bash
python3 replicate.py "a robot walking through a forest" --video --model minimax --name robot-walk
```

**Animate with motion prompt:**
```bash
python3 replicate.py "gentle breathing motion" --video --model animate-diff --image portrait.png --name breathing
```

---

## Provider 3: Midjourney (Discord Automation)

Best for: **childlike, hand-drawn, artistic, crayon-style** illustrations.

```bash
cd ~/.claude/plugins/ormus-illustrator/providers && python3 midjourney.py "PROMPT" --name FILENAME
```

**Single image:**
```bash
python3 midjourney.py "childlike crayon drawing of a happy sun, wobbly lines, bright colors --v 6.1 --s 200" --name happy-sun
```

**Batch generation (JSON file):**
```bash
python3 midjourney.py --batch prompts.json --output ~/project/assets/
```

**Batch JSON format:**
```json
[
  {"name": "sun", "prompt": "childlike crayon sun --v 6.1"},
  {"name": "heart", "prompt": "hand-drawn heart, marker style --v 6.1"}
]
```

**Midjourney parameters:**
- `--v 6.1` - Latest version (default)
- `--niji 6` - Anime style
- `--style raw` - Less stylization
- `--s 0-1000` - Stylization amount (lower = more literal)
- `--ar 1:1` - Aspect ratio

**First-time setup:**
1. Run the script - browser window opens
2. Log into Discord manually
3. Session is saved for future use

---

## Quick Decision Guide

### Images
| Want | Use |
|------|-----|
| Fast draft | Replicate (`flux-schnell`) |
| SVG/vectors | Replicate (`recraft-svg`) |
| Text in image | Replicate (`ideogram-v2`) |
| Childlike/hand-drawn | **Midjourney** |
| Botanical/scientific | Gemini |
| Artistic/ethereal | **Midjourney** |

### Video & Animation
| Want | Use |
|------|-----|
| Animate still image | Replicate (`svd`, `animate-diff`) |
| Text to video | Replicate (`minimax`, `ltx-video`) |
| Talking head / face | Replicate (`liveportrait`) |
| Pixel art animations | **PixelLab** |
| Walk cycles/sprites | **PixelLab** |
| Retro game art | **PixelLab** |

---

## Output Locations

- **Default**: `~/.claude/plugins/ormus-illustrator/output/`
- **Per-project**: Use `--output "PATH"` flag

---

## Troubleshooting

| Error | Provider | Solution |
|-------|----------|----------|
| 401 Unauthorized | Gemini | `gcloud auth application-default login` |
| 429 Rate Limited | Any | Wait 1-2 minutes |
| Discord login needed | Midjourney | Log in manually in browser window |
| Browser not found | Midjourney | `playwright install chromium` |

---

## Example: La Gran Obra Childlike Style

For hand-drawn, crayon-like, blob-shape illustrations:

```bash
# Midjourney prompt for childlike style
python3 midjourney.py "childlike crayon drawing, wobbly organic blob shape, bright yellow color, hand-drawn imperfect lines, no background, simple joyful character, marker texture --v 6.1 --s 150 --ar 1:1" --name yellow-blob --output ~/projects/01-ACTIVE/la-gran-obra/public/assets/shapes/
```

**Style keywords for childlike aesthetic:**
- "childlike crayon drawing"
- "hand-drawn imperfect lines"
- "wobbly organic shapes"
- "marker texture"
- "finger-paint style"
- "simple joyful"
- "no background" or "transparent background"

---

## Provider 4: PixelLab (Pixel Art & Animations)

Best for: **pixel art animations, sprite sheets, walk cycles, idle animations, retro-style game art**.

```bash
cd ~/.claude/plugins/ormus-illustrator/providers && python3 pixellab.py "ACTION" --image REFERENCE.png --name OUTPUT
```

**Generate animation from reference:**
```bash
python3 pixellab.py "walking" --image turpi.png --name turpi-walk --size 64 --frames 4
```

**Generate idle animation:**
```bash
python3 pixellab.py "breathing idle" --image turpi.png --name turpi-idle --frames 8
```

**Generate waving animation:**
```bash
python3 pixellab.py "waving hello" --image turpi.png --name turpi-wave --size 32
```

**Parameters:**
| Flag | Description | Default |
|------|-------------|---------|
| `--image/-i` | Reference image (required) | - |
| `--name/-n` | Output name prefix | animation |
| `--size/-s` | Output size (32, 64, 128) | 64 |
| `--direction/-d` | Character direction (north, south, east, west, etc.) | south |
| `--frames/-f` | Number of frames (2-20) | 4 |
| `--seed` | Random seed for reproducibility | - |
| `--keep-bg` | Keep background (don't remove) | - |
| `--output/-o` | Output directory | default |

**Estimate skeleton from image:**
```bash
python3 pixellab.py --skeleton turpi.png
```

**List animation templates:**
```bash
python3 pixellab.py --list-templates
```

**Available templates:** backflip, breathing-idle, cross-punch, die-and-revive, falling, hook-punch, idle-to-crouch, idle, jump-kick, jump, run, slash, slide, smash-with-sword, spin-kick, walk, wave, wings-flap

**API Key:** Stored in script or set `PIXELLAB_API_KEY` environment variable.

**MCP Integration:** PixelLab MCP server available at `https://api.pixellab.ai/mcp` (configured in project)

---

## Gentle Iteration (No Need to Grind)

Early results might feel rough — totally normal.

But as the loop continues:
- Your prompts become sharper
- The tool begins to "understand" your vibe
- Your mood board gains personality
- A unique style emerges

**You'll notice something special:**
- Claude handles structure
- The image tool handles aesthetics
- You handle taste

---

## The Anti-Patterns

| Don't | Why |
|-------|-----|
| Jump straight to generation | You lose the thinking phase |
| Overload mood boards | Cluttered board = unclear direction |
| Expect instant perfection | Style emerges through iteration |
| Ignore good "accidents" | Sometimes the tool surprises you — save those |
| Fight the tool's personality | Work with its strengths, not against them |

---

## Final Wisdom

This workflow is not about being technical. It's about:

- **Reducing guesswork** in an inherently chaotic process
- **Building a stable creative backbone** with Claude as the consistent partner
- **Letting AI understand your taste** through conversation
- **Growing your style** slowly, naturally, one prompt at a time

It's simple, really. Just a conversation between you and your tools.

No pressure. No heavy theory. Just a path that helps your visual voice grow.

---

*v4.0.0 | Philosophy integrated from LLM-first image generation workflow*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermeticormus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
