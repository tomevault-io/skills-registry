---
name: visual-test
description: Record browser interactions and analyze them with AI vision. Use when you need to validate interaction quality, animation smoothness, or visual authenticity. Use when this capability is needed.
metadata:
  author: shreyfirst
---

# Visual Test - AI-Powered Interaction Analysis

Record browser interactions in a headless browser, capture video, and analyze with Gemini 3 Pro for detailed visual feedback.

## Why This Exists

Building physical, tactile interfaces requires **seeing** what the user sees. This tool lets you:
- Test interactions without manual observation
- Get AI feedback on animation smoothness
- Validate if something "feels" physical vs digital
- Iterate on micro-interactions with objective analysis

## Setup

Dependencies are already installed. Chromium browser is downloaded automatically.

**Environment Variable Required:**

Set `OPENROUTER_API_KEY` in your `.env` file (project root) or export it directly:

```bash
export OPENROUTER_API_KEY=sk-or-v1-your-key-here
```

The shell wrappers automatically load from `.env` if present.

## Usage

### Basic Test (Pre-scripted Actions)

```bash
.cursor/skills/visual-test/scripts/record-and-analyze.sh \
  "http://localhost:8000" \
  "Does the TV knob feel physical when dragged?"
```

### Interactive Test (AI Explores Autonomously)

```bash
.cursor/skills/visual-test/scripts/record-interactive.sh \
  "http://localhost:8000" \
  "When I drag the TV channel knob to the right, describe EXACTLY: the rotation animation timing in milliseconds, whether it uses easing or is linear, if there's visual feedback like a glow or highlight, how smooth the rotation is frame-by-frame, and whether it feels like a physical object with weight or a digital slider" \
  50
```

**CRITICAL: Your prompt must be EXTREMELY SPECIFIC.**

The AI will answer EXACTLY what you ask. If you ask vague questions, you get vague answers.

❌ **Bad:** "Test the knob"  
✓ **Good:** "Drag the knob 100px right and describe the exact rotation speed, easing curve, visual feedback, and whether it overshoots or stops abruptly"

❌ **Bad:** "Check channel transitions"  
✓ **Good:** "Click channel up button and describe frame-by-frame: does old content fade or cut, is there static between channels, timing of the transition in ms, color of any effects, and does it feel like a real TV channel change from 1987"

The AI will:
- Take screenshots after each action
- Decide what to click/drag/type based on your goal
- Execute actions autonomously  
- Record everything (up to 50 steps by default)
- Analyze the final video WITH EXTREME FOCUS on your specific question

### With Interactions

```bash
.cursor/skills/visual-test/scripts/record-and-analyze.sh \
  "http://localhost:8000" \
  "Does the channel change transition look authentic?" \
  '[
    {"type":"click","selector":"#channel-knob"},
    {"type":"drag","selector":"#channel-knob","x":100},
    {"type":"wait","ms":2000}
  ]'
```

## Action Types

| Action | Description | Parameters |
|--------|-------------|------------|
| `click` | Click an element | `selector` |
| `drag` | Drag an element | `selector`, `x`, `y` (offset), `pause` (ms after) |
| `type` | Type text into input | `selector`, `text` |
| `hover` | Hover over element | `selector` |
| `scroll` | Scroll page | `y` (pixels) |
| `wait` | Pause | `ms` (milliseconds) |

## Example Actions

### Test TV Knob Rotation

```json
[
  {"type":"hover","selector":"#tv-knob"},
  {"type":"drag","selector":"#tv-knob","x":200,"pause":1000},
  {"type":"wait","ms":2000}
]
```

### Test Channel Surfing

```json
[
  {"type":"click","selector":"#channel-up"},
  {"type":"wait","ms":1500},
  {"type":"click","selector":"#channel-up"},
  {"type":"wait","ms":1500},
  {"type":"click","selector":"#channel-down"}
]
```

### Test VHS Tape Insertion

```json
[
  {"type":"drag","selector":".vhs-tape","x":0,"y":150,"pause":2000},
  {"type":"wait","ms":3000}
]
```

## Output

- **Video**: Saved to `.cursor/skills/visual-test/recordings/*.webm`
- **Analysis**: Saved to `.cursor/skills/visual-test/recordings/*-analysis.txt`

Videos are kept for review. Clean up old recordings with:

```bash
rm .cursor/skills/visual-test/recordings/*.webm
```

## What the AI Analyzes

The prompt asks Gemini to evaluate:
1. **Visual quality** - Does it look real/authentic?
2. **Interaction feel** - Does it feel physical/mechanical?
3. **Animation smoothness** - Any jank or abrupt changes?
4. **Specific improvements** - What needs fixing?

## Iteration Workflow

1. Build interaction
2. Run visual test with specific prompt
3. Read AI feedback
4. Make adjustments
5. Re-test until it feels right

## Tips for Effective Testing

### 1. **Be EXTREMELY Specific in Your Goal**

The AI answers exactly what you ask. Vague questions = vague answers.

**Examples of Effective Goals:**

```bash
# Testing knob physics
"Drag the #channel-knob element 150 pixels to the right. Describe: 
- Exact rotation speed and timing
- Whether it uses cubic-bezier easing or is linear
- If there's overshoot/bounce at the end
- Visual feedback (glow, shadow, highlight)
- Does it feel like it has inertia and weight?"

# Testing transitions
"Click the 'next channel' button. Describe frame-by-frame:
- How does the old content disappear (fade, cut, slide)?
- Is there static or noise between channels?
- Exact timing in milliseconds
- Color and visual quality of any effects
- Does it look like a real VHS/CRT transition from 1985?"

# Testing tactile feel
"Click the VHS eject button and describe:
- Does the button depress visually?
- Is there shadow/depth change?
- Timing of the press (instant or gradual)?
- Does the tape animate out? How fast?
- Does it FEEL mechanical or digital?"
```

### 2. **Test Micro-Interactions, Not Full Flows**

Focus on one specific interaction at a time:
- One button click
- One drag gesture  
- One transition
- One animation

### 3. **Include What to Look For**

Tell the AI what aspects matter:
- Timing/speed
- Easing curves
- Visual effects
- Physical realism
- Retro authenticity

### 4. **Watch Videos Yourself**

AI analysis + your eye = complete picture. Videos saved in `recordings/`

## Advanced: Programmatic Use

```javascript
import { recordAndAnalyze } from './.cursor/skills/visual-test/scripts/record-and-analyze.js';

const result = await recordAndAnalyze(
  'http://localhost:8000',
  'Does the CRT screen glow feel authentic?',
  [
    {type: 'hover', selector: '#tv-screen'},
    {type: 'wait', ms: 2000}
  ]
);

console.log(result.analysis);
// Access: result.videoPath, result.analysisPath
```

## Troubleshooting

**"OPENROUTER_API_KEY environment variable is required"**
- Add your key to `.env` in project root: `OPENROUTER_API_KEY=sk-or-v1-...`
- Or export directly: `export OPENROUTER_API_KEY=sk-or-v1-...`

**"Executable doesn't exist"**
```bash
cd .cursor/skills/visual-test
npx playwright install chromium
```

**"Selector not found"**
- Action tried to interact with element that doesn't exist
- Check selector with browser DevTools first

**"No video generated"**
- Playwright needs time to finalize video after context.close()
- Script waits automatically, but very fast interactions might need longer wait

## Example Output

```
🌐 Navigating to: http://localhost:8000
🎬 Recording interactions...
  → click on #channel-knob
  → drag on #channel-knob
  → wait on page
💾 Saving video...
📹 Video saved: .cursor/skills/visual-test/recordings/abc123.webm
🤖 Analyzing with Gemini 3 Pro...

================================================================================
📊 ANALYSIS
================================================================================
The knob rotation feels mechanical but lacks physical weight. The easing
curve is too linear - real knobs have inertia. Recommendation: Add cubic-bezier
easing and slight overshoot on release.
================================================================================
```

Use this tool proactively when building retro/physical interfaces. Don't guess if something feels right - test it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shreyfirst) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
