---
name: brand-namer
description: Generate creative, brandable names for products, companies, or projects using proven naming techniques including portmanteaus, phonetic respelling, tech prefixes and suffixes, and industry-specific patterns. Use when brainstorming names for a new venture, product, app, or brand. Use when this capability is needed.
metadata:
  author: devvlabbs
---

# Brand Namer

Generate creative, memorable brand names using proven naming techniques, providing 15-20 options with domain availability patterns, pronunciation guides, and rationale for each.

## When to Use This Skill

- Naming a new startup or company
- Creating a product or feature name
- Rebranding an existing business
- Naming an app or software tool
- Creating a project or side hustle name
- Generating names for a clone/rebrand venture

## Naming Techniques

Reference `references/naming-techniques.md` for comprehensive examples by industry.

### 1. Portmanteau (Word Blending)

Combine two relevant words into a single brandable name.

**Formula**: [Word 1] + [Word 2] = [Blended Name]

| Example | Words | Result |
|---------|-------|--------|
| Trading + Social | Pip + Popular | **Pipular** |
| Finance + Multi-service | Finance + Octopus | **FinTopus** |
| Investment + Growth | Invest + Ignite | **Investnite** |
| Speed + Trading | Bit + Instant | **Binstant** |

### 2. Phonetic Respelling

Take a common word and spell it uniquely while maintaining pronunciation.

**Formula**: [Common Word] → [Unique Spelling]

| Original | Respelled | Notes |
|----------|-----------|-------|
| Voodoo | **Vodu** | Mystical, short |
| Sticker | **Sticka** | Approachable |
| Trader | **Tradr** | Modern, tech |
| Clever | **Clevr** | Smart feel |
| Flicker | **Flickr** | Photo pattern |

### 3. Apple "i" Prefix

Add the tech-credibility "i" prefix to industry terms.

**Formula**: i + [Industry Term]

| Base | Result | Industry |
|------|--------|----------|
| Funds | **iFunds** | Finance |
| Trade | **iTrade** | Trading |
| Folio | **iFolio** | Investing |
| Gym | **iGym** | Fitness |
| Mentor | **iMentor** | Education |

### 4. Tech Suffixes

Add modern suffixes for a contemporary tech feel.

**Common Suffixes**: -ix, -ly, -ify, -io, -hub, -lab

| Base | Suffix | Result |
|------|--------|--------|
| Trade | -ix | **Tradeix** |
| Invest | -ly | **Investly** |
| Stock | -ify | **Stockify** |
| Fund | -io | **Fundio** |
| Market | -hub | **Markethub** |
| Code | -lab | **Codelab** |

### 5. Industry Terms + Twist

Take familiar industry jargon and add creative flair.

| Industry Term | Twist | Result |
|---------------|-------|--------|
| Pips + Ticks | Casual | **PipsTicks** |
| Bull + Bear | Combined | **BullBear** |
| Green Candle | Color focus | **GreenWick** |
| Rep Count | Respell | **RepKount** |

### 6. Gaming/Casual Slang

Make the brand approachable with casual terminology.

| Slang | Application | Result |
|-------|-------------|--------|
| Noob | Beginner focus | **NoobTrader** |
| Level Up | Progression | **LevelUpFunds** |
| Game On | Excitement | **GameOnFit** |
| Pro Mode | Expert tier | **ProModeTrading** |

## Generation Process

### Step 1: Gather Context

Collect from the user:
- **Industry/Niche**: What field is this for?
- **Target Audience**: Who will use this?
- **Tone**: Professional, playful, technical, friendly?
- **Keywords**: Any must-include terms or themes?
- **Avoid**: Any names or styles to avoid?
- **Domain Priority**: .com required or alternatives OK?

### Step 2: Generate Names

Produce 15-20 names across all techniques:

```markdown
## Generated Names for [Project]

### Portmanteau Names
| Name | Pronunciation | Domain Pattern | Why It Works |
|------|---------------|----------------|--------------|
| [Name] | [foh-NEH-tik] | .com/.io likely | [Explanation] |

### Phonetic Respelling
| Name | Pronunciation | Domain Pattern | Why It Works |
|------|---------------|----------------|--------------|

### Tech Prefix/Suffix
| Name | Pronunciation | Domain Pattern | Why It Works |
|------|---------------|----------------|--------------|

### Industry + Twist
| Name | Pronunciation | Domain Pattern | Why It Works |
|------|---------------|----------------|--------------|

### Casual/Gaming Style
| Name | Pronunciation | Domain Pattern | Why It Works |
|------|---------------|----------------|--------------|
```

### Step 3: Domain Availability Verification

Reference `references/domain-availability-guide.md` for TLD strategies.

**Initial Assessment:**
- **Likely Available**: Unique portmanteau, uncommon respelling
- **Check Required**: Common pattern, short name
- **Likely Taken**: Dictionary word, popular pattern

**IMPORTANT: Always verify with WHOIS lookup before confirming availability.**
Initial assessments are educated guesses. Run `whois [domain]` to confirm.

**Recommended TLDs by Type:**
- **.com** - Universal, highest value
- **.io** - Tech/developer products
- **.co** - Startup-friendly alternative
- **.app** - Mobile applications
- **.ai** - AI/ML products
- **.dev** - Developer tools

### Step 4: Brand Conflict Search

Before recommending any name, check for existing brands:

**Search These Sources:**
1. **App Stores** - Search Apple App Store and Google Play for exact name
2. **Google Search** - "[name] app", "[name] company", "[name] [industry]"
3. **Social Media** - Check if @name handles exist on Twitter, Instagram
4. **Crunchbase/LinkedIn** - Search for existing companies
5. **Trademark Database** - USPTO.gov for US marks

**Red Flags:**
- Existing app in same category (even different spelling)
- Established company with similar name in adjacent industry
- Active social handles with significant following
- Registered trademark in same class

**Example:** "Pickster" seemed good but had an NFL Pick'em app by Pickster LLC - same sports/predictions space = conflict.

### Step 5: Reputation Check (If Name Exists Elsewhere)

If a similar brand exists in a different industry, check its reputation:

**Check:**
- Trustpilot reviews
- Sitejabber ratings
- BBB complaints
- Google "[name] reviews" or "[name] scam"

**Why This Matters:** If "Winster.com" has 1.8-star reviews for a gaming site, you don't want that association even if your app is different.

### Step 6: Misspelling & Variation Strategy

**When Misspellings Work:**
- One letter change with same pronunciation (Pickster → **Pikster**)
- Dropped vowel that's still readable (Flickr, Tumblr)
- Letter swap that looks intentional (Quick → Kwik)

**When Misspellings DON'T Work:**
- Multiple letter changes (hard to remember)
- Changes that affect pronunciation
- Random number additions (Pick3ter)
- Similar to existing brand's misspelling
- Creates confusion about how to spell in word-of-mouth

**The "Radio Test":** If someone says the name out loud, can the listener find your website? If "Pikster" is said, they might try pickster.com first - but they'll find you at pikster.ai quickly.

### Step 7: Top Recommendations

```markdown
## Top 5 Recommendations

### 1. [Name] - Top Pick
- **Pronunciation**: [phonetic guide]
- **Technique**: [which technique used]
- **Domain**: [verified availability status]
- **Brand Conflicts**: [search results summary]
- **Why It Works**: [detailed explanation]
- **Considerations**: [any potential issues]
```

### Step 8: Iterative Refinement

Brand naming is rarely one-shot. Expect iteration:

1. **First pass**: Generate 15-20 names across techniques
2. **User narrows**: User picks 2-3 favorites
3. **Deep verification**: Check domains, brand conflicts, reputation
4. **Issues found**: Often top picks have problems (taken domain, existing brand)
5. **Pivot**: Generate variations or new names based on what user liked
6. **Final selection**: User picks from verified options

**Key Lesson:** Don't promise a name is available until you've verified it. Always present initial names as "likely available" pending verification.

## Example Output

### Request
```
Generate brand names for a forex trading education platform
targeting beginners. Tone should be friendly but credible.
```

### Response

## Brand Names for Forex Education Platform

### Portmanteau Names
| Name | Pronunciation | Domain | Why It Works |
|------|---------------|--------|--------------|
| Pipular | PIP-yoo-lar | .com likely | "Pip" (forex) + "popular" = accessible trading |
| Forexify | for-EX-ih-fy | .com check | Forex + -ify = transformation/learning |
| Tradelingo | TRADE-ling-go | .com check | Trade + lingo = learn the language |
| Fxcademy | eff-ex-CAD-eh-mee | .com likely | FX + academy = clear education |

### Phonetic Respelling
| Name | Pronunciation | Domain | Why It Works |
|------|---------------|--------|--------------|
| Tradr | TRAY-der | .io likely | Modern, memorable, short |
| Piplrn | PIP-learn | .com likely | Pip + learn compressed |
| Fxr | EFF-ex-er | .io likely | Minimalist forex reference |

### Tech Suffix Names
| Name | Pronunciation | Domain | Why It Works |
|------|---------------|--------|--------------|
| Pipix | PIP-ix | .com likely | Pip + ix = technical feel |
| Tradely | TRADE-lee | .com check | Action-oriented, approachable |
| Forexio | for-EX-ee-oh | .com check | Platform feel |

### Industry + Twist
| Name | Pronunciation | Domain | Why It Works |
|------|---------------|--------|--------------|
| GreenPip | GREEN-pip | .com check | Green = profit, positive |
| PipSchool | PIP-school | .com check | Clear educational positioning |
| BullishU | BULL-ish-you | .com likely | Bullish + University |

### Gaming/Casual Style
| Name | Pronunciation | Domain | Why It Works |
|------|---------------|--------|--------------|
| NoobTrader | NOOB-tray-der | .com likely | Self-aware beginner focus |
| LevelUpFx | level-up-EFF-ex | .com likely | Progression metaphor |
| TradingQuest | TRAY-ding-kwest | .com check | Journey/adventure feel |

---

## Top 5 Recommendations

### 1. Pipular - Top Pick
- **Pronunciation**: PIP-yoo-lar
- **Technique**: Portmanteau (pip + popular)
- **Domain**: pipular.com likely available
- **Why It Works**: "Pip" instantly signals forex to traders. "Popular" implies accessible, mainstream. Friendly sound appeals to beginners. Easy to spell and remember.
- **Considerations**: Verify no trademark conflicts

### 2. Tradely
- **Pronunciation**: TRADE-lee
- **Technique**: Tech suffix (-ly)
- **Domain**: Check tradely.com, tradely.io
- **Why It Works**: Follows successful patterns (Grammarly feel). Action-oriented. Approachable for beginners.
- **Considerations**: May be taken, check variations

### 3. NoobTrader
- **Pronunciation**: NOOB-tray-der
- **Technique**: Gaming slang
- **Domain**: noobtrader.com likely available
- **Why It Works**: Self-aware humor appeals to beginners. Removes intimidation. Memorable and shareable.
- **Considerations**: Some may find "noob" unprofessional

### 4. Fxcademy
- **Pronunciation**: eff-ex-CAD-eh-mee
- **Technique**: Portmanteau (FX + academy)
- **Domain**: fxcademy.com likely available
- **Why It Works**: Clear educational positioning. "FX" is standard abbreviation. Professional yet accessible.
- **Considerations**: Similar to existing forex academies

### 5. GreenPip
- **Pronunciation**: GREEN-pip
- **Technique**: Industry term + twist
- **Domain**: greenpip.com check
- **Why It Works**: "Green" = profit (green candles). Positive, optimistic feeling. Visual branding potential.
- **Considerations**: Verify no existing brands

---

## Next Steps

1. **Verify domains** with WHOIS lookup (not just guessing)
2. **Search App Stores** for existing apps with same/similar name
3. **Google the name** + your industry keywords
4. **Check social handles** (@name on Twitter, Instagram)
5. **Trademark search** at USPTO.gov
6. **Reputation check** if similar brand exists elsewhere
7. **Register quickly** - good names get taken fast

## Tips for Best Results

- **Share industry context**: Names work better when tailored
- **Specify tone preferences**: Professional vs. playful matters
- **Mention competitors**: Helps avoid similar names
- **List keywords**: Include must-have or must-avoid terms
- **State domain requirements**: .com only or alternatives OK

## Resources

This skill includes reference documentation:
- `references/naming-techniques.md` - Detailed patterns with 50+ examples per industry
- `references/domain-availability-guide.md` - TLD selection and checking strategies

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devvlabbs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
