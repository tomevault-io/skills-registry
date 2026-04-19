---
name: japanese-learning-game
description: Create effective Japanese learning games with SRS (Spaced Repetition System), audio-first approach, and gamification elements. Use this skill when building vocabulary flashcards, conversation practice games, or interactive quiz games for Japanese learners (JLPT N5-N3). Includes ready-to-use templates, word databases, and conversation scenarios. Use when this capability is needed.
metadata:
  author: coolchang
---

# Japanese Learning Game Skill

## Overview

Create engaging, effective Japanese learning games that combine scientifically-proven Spaced Repetition System (SRS) with gamification and audio-first learning. Generate complete React-based web applications optimized for vocabulary acquisition, conversation practice, and long-term retention.

This skill provides:
- **SRS Algorithm**: SuperMemo SM-2 implementation for optimal review scheduling
- **Game Templates**: Ready-to-use React components for flashcards, quizzes, typing games, and conversation simulations
- **Learning Content**: JLPT-leveled vocabulary (N5-N3) and conversation scenarios
- **Gamification**: XP, levels, badges, and daily streak systems
- **Audio Integration**: Text-to-speech and audio playback for pronunciation practice

## When to Use This Skill

Activate this skill when the user requests:

- "일본어 단어 학습 게임 만들어줘"
- "N5 단어로 플래시카드 게임 생성"
- "회화 연습 게임 만들어줘"
- "JLPT 학습 앱 만들어줘"
- "음식 관련 일본어 단어 퀴즈 게임"
- "SRS 기반 언어 학습 게임"

Any request involving Japanese learning games, vocabulary practice, conversation simulation, or JLPT preparation should trigger this skill.

## Workflow

### Step 1: Understand Requirements

Ask clarifying questions to determine:

1. **Game Type**: What type of game?
   - Flashcard (플래시카드)
   - Quiz (퀴즈)
   - Typing (타이핑 게임)
   - Conversation (회화 연습)
   - All (종합)

2. **Content Scope**: What learning content?
   - JLPT Level (N5, N4, N3, N2, N1)
   - Category (음식, 여행, 일상, 숫자, etc.)
   - Custom words vs. pre-built database

3. **Features**: Which features are needed?
   - SRS system (recommended: yes)
   - Audio support (recommended: yes)
   - Gamification (XP, badges, streaks)
   - Progress tracking
   - Offline support (PWA)

**Example Dialog:**

User: "일본어 음식 단어 학습 게임 만들어줘"

Claude: "네! 음식 관련 일본어 학습 게임을 만들어드리겠습니다. 몇 가지 확인할게요:
1. 어떤 게임 타입을 원하시나요? (플래시카드 / 퀴즈 / 타이핑 / 종합)
2. JLPT 레벨은요? (N5 추천)
3. SRS(간격 반복 학습) 시스템을 포함할까요? (추천: 네)"

User: "플래시카드로 N5 레벨, SRS 포함해주세요"

Claude: "알겠습니다! 바로 생성하겠습니다."

### Step 2: Generate Game Project

Use the game scaffolder script to create the project:

```bash
python scripts/game_scaffolder.py \
  --game-type flashcard \
  --jlpt-level N5 \
  --category food \
  --output ./japanese-food-game
```

The script will:
1. Copy the React template from `assets/game-template/`
2. Inject vocabulary data from `references/vocabulary/n5-words.json`
3. Filter words by category (food)
4. Add game-specific components
5. Create configuration file `game.config.json`

**What Gets Created:**
```
japanese-food-game/
├── package.json           # Dependencies configured
├── vite.config.ts        # Build configuration
├── game.config.json      # Game settings
├── src/
│   ├── data/
│   │   └── vocabulary.json  # Filtered food words
│   ├── components/
│   ├── lib/
│   │   └── srs/
│   │       └── algorithm.ts  # SRS implementation
│   └── ...
└── public/
```

### Step 3: Integrate SRS System

The SRS algorithm is already included. Explain how to use it:

```typescript
import { calculateNextReview, SRSCard } from './lib/srs/algorithm'

// When user answers a card
const handleAnswer = (quality: number) => {
  const updatedCard = calculateNextReview(quality, currentCard)

  // Save to storage
  saveCardProgress(updatedCard)

  // quality scale:
  // 5: Perfect (즉시 정답)
  // 4: Correct (약간 고민)
  // 3: Difficult (어렵게 정답)
  // 2: Wrong but familiar (틀렸지만 알 것 같음)
  // 1: Wrong (틀림)
  // 0: No idea (전혀 모름)
}
```

The SRS algorithm from `scripts/srs_algorithm.py` needs to be ported to TypeScript and placed in the game template.

### Step 4: Add Audio Support

Integrate Web Speech API or audio files:

```typescript
// Text-to-Speech using Web Speech API
const speak = (text: string, lang: string = 'ja-JP') => {
  const utterance = new SpeechSynthesisUtterance(text)
  utterance.lang = lang
  utterance.rate = 0.9  // Slightly slower for learning
  speechSynthesis.speak(utterance)
}

// Usage in flashcard
<button onClick={() => speak(card.word)}>
  🔊 発音を聞く
</button>
```

For pre-recorded audio, reference the audio files in vocabulary data.

### Step 5: Customize and Enhance

Common customizations:

**1. Add More Vocabulary**
- Edit `src/data/vocabulary.json`
- Or add to `references/vocabulary/` and regenerate

**2. Adjust SRS Settings**

Edit `game.config.json`:
```json
{
  "srs": {
    "newCardsPerDay": 30,      // Increase daily new cards
    "reviewCardsPerDay": 150   // Increase review limit
  }
}
```

**3. Customize Gamification**
```json
{
  "gamification": {
    "xp": true,
    "levels": true,
    "badges": true,
    "dailyStreak": true,
    "leaderboard": false  // Disable competitive features
  }
}
```

**4. Add Custom Conversation Scenarios**

Copy from `references/conversations/` or create new ones following the schema.

### Step 6: Build and Deploy

```bash
cd japanese-food-game
npm install
npm run dev      # Development
npm run build    # Production build
```

Deploy to:
- **Vercel**: `vercel deploy`
- **Netlify**: Drag `dist/` folder
- **GitHub Pages**: Use gh-pages

## Resources

### scripts/

**`srs_algorithm.py`** - Spaced Repetition System implementation
- Run standalone: `python scripts/srs_algorithm.py --demo`
- Import into TypeScript/JavaScript via TypeScript port
- Based on SuperMemo SM-2 algorithm

**`game_scaffolder.py`** - Game project generator
- Creates complete React project
- Injects vocabulary data
- Configures game type and settings

### references/

**`vocabulary/`** - JLPT-leveled word databases
- `n5-words.json` - N5 words (20 sample words included)
- `n4-words.json` - N4 words (to be added)
- `n3-words.json` - N3 words (to be added)

Each word includes:
- Japanese word, reading, romaji
- Korean meaning
- Part of speech, category, JLPT level
- Example sentences
- Difficulty and frequency ratings

**`conversations/`** - Scenario-based conversation practice
- `restaurant-ordering.json` - Restaurant conversation
- More scenarios to be added

Each scenario includes:
- Branching dialogue trees
- Multiple choice responses
- Feedback and explanations
- Key phrases and cultural notes

### assets/

**`game-template/`** - React boilerplate
- Complete Vite + React + TypeScript setup
- Tailwind CSS configured
- Essential dependencies included
- PWA support via vite-plugin-pwa

**`sounds/`** - Audio effects (to be added)
- `correct.mp3` - Correct answer sound
- `wrong.mp3` - Wrong answer sound
- `levelup.mp3` - Level up fanfare

## Examples

### Example 1: Basic Flashcard Game

User: "N5 단어로 플래시카드 게임 만들어줘"

Steps:
1. Run game scaffolder:
   ```bash
   python scripts/game_scaffolder.py \
     --game-type flashcard \
     --jlpt-level N5 \
     --output ./n5-flashcard
   ```
2. Install and run:
   ```bash
   cd n5-flashcard
   npm install
   npm run dev
   ```
3. Open http://localhost:5173

### Example 2: Restaurant Conversation Practice

User: "레스토랑 회화 연습 게임 만들어줘"

Steps:
1. Generate conversation game:
   ```bash
   python scripts/game_scaffolder.py \
     --game-type conversation \
     --jlpt-level N5 \
     --output ./restaurant-practice
   ```
2. Use pre-built conversation scenario (already included)
3. Run the game

### Example 3: Comprehensive Learning App

User: "일본어 종합 학습 앱 만들어줘 - 단어, 퀴즈, 회화 다 포함"

Steps:
1. Create all-in-one app:
   ```bash
   python scripts/game_scaffolder.py \
     --game-type all \
     --jlpt-level N5 \
     --output ./japanese-learning-app
   ```
2. Customize game.config.json to enable all features
3. Add multiple vocabulary categories
4. Add multiple conversation scenarios
5. Deploy as PWA for mobile use

## Tips and Best Practices

1. **Start Small**: Begin with N5 level and expand
2. **Audio First**: Always enable audio for pronunciation
3. **SRS is Key**: The SRS system is what makes learning stick
4. **Daily Practice**: Encourage 15-20 minutes daily over marathon sessions
5. **Gamification Balance**: Use game elements to motivate, not distract
6. **Progressive Disclosure**: Don't overwhelm beginners with all features at once

## Troubleshooting

**Issue**: Game scaffolder can't find vocabulary data

**Solution**: Check that `references/vocabulary/n5-words.json` exists, or use `--category all` to include sample data

**Issue**: Audio not working

**Solution**: Web Speech API requires user interaction. Add a "Start" button to initialize audio

**Issue**: SRS intervals too aggressive

**Solution**: Adjust easiness factor in `scripts/srs_algorithm.py` (default: 2.5)

## Future Enhancements

- Mobile app (React Native port)
- More JLPT levels (N4, N3, N2, N1)
- Grammar practice games
- Kanji writing practice
- Community features (share decks)
- AI-powered conversation practice

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/coolchang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
