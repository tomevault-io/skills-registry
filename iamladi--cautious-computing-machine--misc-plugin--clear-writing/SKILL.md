---
name: clear-writing
description: Transform any writing into clear, engaging prose. Removes AI patterns, applies Strunk's 18 principles, and adds engagement techniques. Works for technical reports, documentation, emails, social media, and general writing. Use when writing feels flat, verbose, or "AI-generated. Use when this capability is needed.
metadata:
  author: iamladi
---

Make writing clear, direct, and human. Strip the slop. Add rhythm.

Based on Strunk's Elements of Style, Wikipedia's AI Cleanup patterns, and structural techniques for engagement.

## When to Use

- Technical reports that read like textbooks
- Documentation that puts readers to sleep
- Emails that bury the ask in paragraph 4
- Writing that "sounds like AI"
- Any prose that feels verbose or lifeless
- Social media posts that drone
- Commit messages, error messages, UI text

## Content Type Detection

Adapt technique to content:

| Type | Priority | Approach |
|------|----------|----------|
| **Technical report** | Clarity + BLUF | Lead with conclusions, scannable headers |
| **Documentation** | Clarity + examples | Progressive disclosure, concrete samples |
| **Email** | Directness | Ask in first sentence, scannable body |
| **Social media** | Hook + punch | Value in 3 seconds, rhythm matters |
| **General writing** | Balance | Clarity with engagement |

---

## Part I: Strunk's 18 Rules

### Elementary Rules of Usage (1-7)

**Rule 1: Form possessive singular with 's**

Add 's regardless of final consonant: "Charles's friend," "Burns's poems."

Exceptions: ancient names ending in -es/-is (Achilles' heel), Jesus', conscience' sake.

**Rule 2: Use serial comma**

"Red, white, and blue" - comma after each term except the last.

**Rule 3: Enclose parenthetic expressions in commas**

Never insert only one comma when two are required.

Parenthetic: dates, abbreviations (etc., jr.), non-restrictive clauses.
Not parenthetic: restrictive clauses that identify the antecedent.

**Rule 4: Comma before conjunction in coordinate clauses**

"The situation is perilous, but there is still one chance of escape."

**Rule 5: Don't join independent clauses with comma alone**

Use semicolon: "Stevenson's romances are entertaining; they are full of exciting adventures."

Or use a conjunction with comma. Or make two sentences.

**Rule 6: Don't break sentences in two**

Fragments like "Coming home from Liverpool" should attach to the preceding sentence.

Exception: emphatic single words or phrases may stand alone if emphasis is clear.

**Rule 7: Participial phrases must refer to grammatical subject**

"Walking slowly down the road, he saw a woman" - *walking* refers to *he*.

Dangling modifiers produce absurd results.

### Elementary Principles of Composition (8-18)

**Rule 8: One topic per paragraph**

Each new paragraph signals a new step in development.

**Rule 9: Open with topic sentence, end in alignment**

Let readers grasp each paragraph's purpose immediately.

**Rule 10: Use active voice**

| Passive (weak) | Active (strong) |
|----------------|-----------------|
| "The report was written by the team" | "The team wrote the report" |
| "Errors were found in the code" | "We found errors in the code" |
| "The decision was made to proceed" | "We decided to proceed" |
| "It was determined that..." | "We determined..." |

Passive hides the actor. Active shows who does what.

Exception: when the actor is unknown, irrelevant, or when a particular word must become the subject.

**Rule 11: Put statements in positive form**

Make definite assertions. Avoid tame, hesitating language.

| Negative (weak) | Positive (strong) |
|-----------------|-------------------|
| "He was not very often on time" | "He usually came late" |
| "did not remember" | "forgot" |
| "did not pay attention" | "ignored" |
| "did not have confidence in" | "distrusted" |
| "not important" | "trivial" |
| "not honest" | "dishonest" |
| "does not have" | "lacks" |

**Rule 12: Use definite, specific, concrete language**

Abstract numbs. Concrete sticks.

| Vague | Specific |
|-------|----------|
| "A period of unfavorable weather" | "It rained every day for a week" |
| "He showed satisfaction" | "He grinned" |
| "Significant improvements" | "40% faster load time" |
| "Various stakeholders" | "Engineering, sales, and legal" |

**Rule 13: Omit needless words**

A sentence should contain no unnecessary words, a paragraph no unnecessary sentences.

**Cut these:**
- "the fact that" → delete
- "who is", "which was" → delete
- "the question as to whether" → "whether"
- "there is no doubt but that" → "no doubt"
- "in a hasty manner" → "hastily"
- "this is a subject that" → "this subject"
- "the reason why is that" → "because"

**Word math:**
- "owing to the fact that" → "since"
- "in spite of the fact that" → "though"
- "call your attention to the fact that" → "remind you"
- "I was unaware of the fact that" → "I was unaware"

**Rule 14: Avoid succession of loose sentences**

Vary sentence structure. Don't string everything together with *and*, *but*, *so*.

**Rule 15: Express coordinate ideas in parallel form**

Similar thoughts need similar grammatical forms.

| Non-parallel | Parallel |
|--------------|----------|
| "Reading, writing, and how to speak" | "Reading, writing, and speaking" |
| "The report was accurate and written on time" | "The report was accurate and timely" |

**Rule 16: Keep related words together**

Don't split subjects from verbs with lengthy modifiers.

| Split | Together |
|-------|----------|
| "The dog, despite being tired and hungry after the long journey, ran home" | "Despite being tired and hungry after the long journey, the dog ran home" |
| "He only found two errors" | "He found only two errors" |

**Rule 17: Use consistent tense in summaries**

Present tense for dramatic summaries. Avoid repetitive "he said" attributions.

**Rule 18: Place emphatic words at sentence end**

Sentences build to their close. Save the punch for last.

| Weak ending | Strong ending |
|-------------|---------------|
| "Humanity has hardly advanced in the last century, according to some philosophers" | "According to some philosophers, humanity has hardly advanced in the last century" |
| "This method is faster, which we discovered" | "We discovered this method is faster" |

---

## Part II: AI Pattern Elimination

LLMs use statistical algorithms to predict likely next words. This creates patterns that appear across AI-generated text. These patterns aren't inherently wrong - they're just predictable. Detection is about removing *undisclosed* AI artifacts, not all AI assistance.

### Regression to the Mean

AI tends toward generic, statistically common outputs rather than specific facts:
- Vague claims about importance and legacy
- Generic positive language replacing specific details
- Superficial connections to broader topics

**Fix:** Be specific rather than grandiose. Describe what something actually does.

### Puffery Words (Delete or Replace)

These words inflate without adding meaning:

```
pivotal, crucial, vital, essential, groundbreaking, seamless, robust,
revolutionary, cutting-edge, state-of-the-art, innovative, dynamic,
comprehensive, holistic, synergistic, leveraged, optimized, streamlined,
game-changing, paradigm-shifting, world-class, best-in-class, premium,
transformative, unprecedented, remarkable, stunning, breathtaking
```

Replace with specifics or delete entirely.

### Overused AI Vocabulary

**High-frequency slop:**
```
delve, leverage, multifaceted, foster, tapestry, testament,
landscape, paradigm, ecosystem, framework, nuanced, robust,
dynamic, synergy, utilize, facilitate, enhance, optimize,
seamlessly, holistically, strategically, align with, crucial,
emphasize, underscore, vibrant
```

**Plain alternatives:**
| AI word | Human word |
|---------|------------|
| delve | look into, examine, explore |
| leverage | use |
| utilize | use |
| facilitate | help, enable |
| enhance | improve |
| optimize | improve |
| multifaceted | complex (or be specific) |
| robust | strong (or say why it's reliable) |
| seamlessly | smoothly (or describe the actual experience) |
| align with | match, support, follow |
| underscore | show, reveal |

### Empty -ing Constructions

Pattern: "verb-ing" + abstract noun. Almost always delete.

```
ensuring reliability → (just describe what happens)
showcasing features → (just list them)
highlighting significance → (just state it)
demonstrating capability → (just show it)
fostering collaboration → (just describe the process)
enabling transformation → (just say what changes)
facilitating communication → (people talk)
enhancing performance → (say how much faster)
reflecting broader trends → (say what trend)
```

This is "superficial analysis" - inanimate facts described as "highlighting" or "reflecting" significance. A narrator's claim, not actual developments.

### Promotional Travel-Brochure Language

```
nestled, breathtaking, stunning, remarkable, boasts, continues to captivate,
stands as a testament, beacon of, rich tapestry of, harmonious blend,
embodies the essence of, sheer beauty, truly unique, hidden gem,
continues to attract, steeped in history
```

If it sounds like a hotel website, cut it.

### Didactic Disclaimers (Delete)

These add nothing:

```
"It's important to note that..."
"It's worth mentioning that..."
"In today's world..."
"In the context of..."
"As we all know..."
"It goes without saying..."
"Needless to say..."
"For all intents and purposes..."
"At the end of the day..."
"When all is said and done..."
"It is crucial to understand..."
```

Just say the thing.

### Rule of Three Abuse

AI loves tripling adjectives. Cut to one (the best one):

| AI pattern | Fix |
|------------|-----|
| "innovative, cutting-edge, revolutionary" | Pick ONE or be specific |
| "fast, efficient, and reliable" | "reliable" or "completes in 2ms" |
| "simple, intuitive, and easy-to-use" | "simple" |

### Zombie Nouns (Nominalizations)

Nouns made from verbs. They deaden prose.

| Zombie | Alive |
|--------|-------|
| optimization | optimize |
| implementation | implement |
| utilization | use |
| consideration | consider |
| documentation | document |
| notification | notify |
| transformation | transform |
| facilitation | help |
| communication | communicate |
| examination | examine |
| investigation | investigate |
| determination | determine |

**Hunt endings:** -ion, -ment, -ance, -ence, -ity

Example:
- "The implementation of the optimization required consideration"
- → "We considered how to implement the optimization"
- → Better: "We optimized it"

### Negative Parallelisms

"Not only...but also" appears excessively in AI text. Often inappropriate for neutral tone. Use sparingly.

### Elegant Variation (False Synonyms)

Changing terms for variety creates confusion:
- "The system" → "the platform" → "the solution" → "the tool"
- Pick one term and stick with it

### False Ranges

AI creates fake spectrums:
- "from the mundane to the profound"
- "whether simple or complex"

Often meaningless. Cut.

### Hedging Language (Cut or Commit)

```
somewhat, arguably, relatively, generally, typically, essentially,
virtually, basically, fundamentally, sort of, kind of, in a sense,
to some extent, for the most part, more or less, it could be argued
```

Either commit to the claim or cut it.

### Copula Avoidance

AI sometimes avoids simple "is/are" statements, adding unnecessary complexity. "The building is tall" beats "The building stands as a towering presence."

### Chatbot Correspondence Artifacts

Remove when present:
- "I hope this helps!"
- "Let me know if you have questions"
- "Here's what I found"
- Collaborative phrases written *for the user* rather than *as content*

### Formatting Artifacts

| Pattern | Issue |
|---------|-------|
| Excessive boldface | Use sparingly for emphasis |
| Inline-header lists | Boldfaced headers with colons |
| Em dash overuse (—) | Reserve for intentional effect |
| Decorative emojis | Never in formal writing |
| Title Case In Every Heading | Use sentence case |

---

## Part III: Words and Expressions Commonly Misused

Quick reference for common errors. See `elements-of-style/05-words-commonly-misused.md` for full list.

| Word | Guidance |
|------|----------|
| **affect/effect** | Affect = influence (verb). Effect = result (noun) or bring about (verb) |
| **as to whether** | Just use "whether" |
| **can/may** | Can = able. May = permitted |
| **case** | Usually unnecessary. "Many rooms were poorly ventilated" not "In many cases..." |
| **character** | Often redundant. "Hostile acts" not "Acts of a hostile character" |
| **claim** | Don't substitute for declare, maintain, or charge |
| **compare to/with** | To = resemblance between different things. With = differences between similar things |
| **data** | Plural (like phenomena, strata) |
| **different from** | Not "different than" |
| **due to** | Only as predicate/modifier. Use "because of" in adverbial phrases |
| **factor** | Hackneyed. "He won by training harder" not "Training was a factor" |
| **feature** | Usually adds nothing |
| **however** | Don't start sentences with it (in meaning "nevertheless") |
| **less/fewer** | Less = quantity. Fewer = number. "Fewer errors" not "less errors" |
| **like/as** | Like governs nouns. As for phrases/clauses. "We spent the evening as in the old days" |
| **literally** | Don't use to support exaggeration |
| **possess** | Don't substitute for have/own |
| **state** | Don't substitute for say/remark. Restrict to "express fully" |
| **very** | Use sparingly. Employ inherently strong words |
| **while** | Restrict to "during the time that." Don't substitute for and/but/although |

---

## Part IV: Engagement Techniques

### BLUF (Bottom Line Up Front)

For technical reports and emails. Lead with the conclusion.

**Structure:**
1. Main conclusion/recommendation (first sentence)
2. Key supporting points (2-3 bullets)
3. Details and evidence (rest of document)

**Example:**
```
BAD:  "After analyzing Q3 metrics across all regions and considering
       various factors including market conditions..."

GOOD: "We should expand to APAC in Q1. Revenue opportunity is $2M with
       6-month payback."
```

### SCR Pattern (Situation-Complication-Resolution)

For narrative flow when BLUF isn't appropriate.

1. **Situation**: Establish context (1-2 sentences)
2. **Complication**: The problem or tension
3. **Resolution**: The answer/recommendation

### SCQA Variant

1. **Situation**: Context
2. **Complication**: What changed
3. **Question**: What we need to decide
4. **Answer**: The recommendation

### Five Hook Types

For openings that grab attention:

1. **Surprising metric**: "73% of users abandon before checkout"
2. **Micro-story**: "Last Tuesday, our deploy broke production for 4 hours"
3. **Question**: "What if your tests could run in 10 seconds?"
4. **Contrast**: "We expected 20% growth. We got 180%."
5. **Time pressure**: "The API deprecates in 30 days"

### 1-3-1 Rhythm

Structure paragraphs:
- 1 strong opening sentence
- 3 explanatory/supporting sentences
- 1 punchy closing sentence

### Sentence Length Audit

Mix short and medium. Avoid long runs.

- **Short (≤8 words)**: For emphasis, transitions, conclusions
- **Medium (~15 words)**: For explanation
- **Long (25+ words)**: Use sparingly, never consecutive

Good rhythm:
```
Short. Then explain in a medium sentence that provides context.
This builds understanding. Medium again for detail. Land it.
```

### Scanner's Contract

Headers must be informative, not generic.

| Generic | Informative |
|---------|-------------|
| "Background" | "Why Manual Testing Fails at Scale" |
| "Results" | "40% Reduction in Deploy Time" |
| "Discussion" | "Trade-offs: Speed vs. Reliability" |
| "Recommendations" | "Migrate to CI/CD by Q2" |

Readers skim headers. Each header should tell them something.

---

## Quick Reference: Transformation Checklist

**Before publishing, verify:**

1. [ ] Lead with conclusion (BLUF)
2. [ ] Active voice throughout
3. [ ] Positive statements (say what *is*, not what isn't)
4. [ ] No puffery words (pivotal, crucial, seamless, robust)
5. [ ] No empty -ing phrases
6. [ ] No zombie nouns (-ion, -ment, -ance)
7. [ ] No hedging without purpose
8. [ ] No rule-of-three adjective stacking
9. [ ] Specific numbers instead of "many" or "significant"
10. [ ] Scannable headers that inform
11. [ ] Varied sentence length
12. [ ] Related words kept together
13. [ ] Emphatic words at sentence end
14. [ ] Every word earns its place
15. [ ] Consistent terminology (no elegant variation)

---

## Anti-Patterns

**Never write prose that:**

- Buries the lead in paragraph 3
- Uses passive voice to hide responsibility
- Stacks adjectives in threes
- Opens with "In today's rapidly evolving landscape..."
- Ends with "...and so much more!"
- Uses "leverage" when "use" works
- Hedges every claim with "somewhat" or "relatively"
- Abstracts concrete things into nominalizations
- Reads like a press release
- Sounds like every other AI-generated doc
- Claims things are "highlighting significance" or "reflecting importance"
- Uses "not only...but also" for neutral statements
- Switches terms for variety (system → platform → solution)

---

## Voice

- Write for a smart reader who's busy
- One idea per sentence
- Short paragraphs (3-5 sentences max)
- Use "you" and "we" — write to someone
- Have opinions — wishy-washy writing wastes time
- If a 7th grader couldn't understand it, simplify
- Specific beats grandiose every time

---

## Reference Files

For detailed coverage, see:
- `elements-of-style/02-rules-of-usage.md` - Grammar and punctuation rules
- `elements-of-style/03-principles-of-composition.md` - Structure and voice
- `elements-of-style/04-matters-of-form.md` - Formatting conventions
- `elements-of-style/05-words-commonly-misused.md` - Word choice guide

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iamladi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
