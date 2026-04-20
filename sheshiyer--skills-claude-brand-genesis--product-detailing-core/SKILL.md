---
name: product-detailing-core
description: ARCHIVE REFERENCE ONLY. Superseded by active `detailed-product-description` and `product-positioning-summary` skills. Use as inspiration; do not invoke in orchestrated flow. Use when this capability is needed.
metadata:
  author: sheshiyer
---

# Product Detailing & Positioning Core

This skill acts as the **Chief Product Officer** and **Brand Architect**. It does not merely describe the product; it deconstructs it, analyzes it against the market, and reconstructs it as a compelling market proposition. It serves as the absolute "Source of Truth" for every downstream task (Copywriting, Ads, Email, Video).

## Input Variables
- **[PRODUCT_LINEUP]**: A raw list of the products (Hero + Accessories), their prices, and basic specs.
- **[PERSONA_DATA]**: The deep-psychology dossier from `buyer-persona-generator`.
- **[COMPETITOR_DATA]**: The intelligence report from `competitor-analysis`.

## The Protocol (The 30-Step Deep Dive)

You must execute this sequence with extreme rigor. Do not rush. Do not summarize.

### Phase 1: Portfolio Architecture & Hierarchy
*Before we detail, we must define the constellation.*

* **Step 1 (The Hero Identification):** Explicitly identify the "Flagship" product. Why is *this* the leader? (e.g., "The Backpack drives the revenue; the pouches drive the margin").
* **Step 2 (The Ecosystem Map):** Map every support product to a specific "User Need" that the Hero product *almost* solves but doesn't fully. (e.g., "Hero carries games; Accessory A protects specific dice").
* **Step 3 (The Interdependency Check):** Define the dependency logic. Is it "Standalone," "Add-on," or "Required"?

### Phase 2: Canonical Engineering (The Hard Truth)
*Act as a Technical Director. Create the unshakeable fact base.*

* **Step 4 (Identity & Pricing):** Define the Canonical Name, Tagline, Category, MRP, and Pre-order Deal. *Constraint:* No hidden tiers.
* **Step 5 (Dimensional Physics):** Document exact dimensions (Internal/External), Weight (Empty/Full), and Capacity.
* **Step 6 (Material Science):** Do not just list materials; describe their grade. (e.g., instead of "Nylon," use "Water-resistant 600D Ballistic Nylon with PU Coating").
* **Step 7 (Connectivity & Power):** (If tech) Define Battery mAh, Bluetooth version, Charging time, and Input/Output specs.
* **Step 8 (Compliance & Safety):** List every required certification (COPPA, CE, FCC) and necessary legal disclaimers.

### Phase 3: The Feature-Benefit-Proof Chain
*Ref: Workshop Part 2 - Detailed Description. Do not just list features. Prove them.*

* **Step 9 (Hero Features):** For the top 5 features, construct a **Triad**:
    * *Feature:* What is it?
    * *Benefit:* What does it do for the user?
    * *Proof:* How do we know it works? (e.g., "Reinforced Stitching" -> "Holds 50lbs" -> "Tested to 1000 cycles").
* **Step 10 (Ecosystem Features):** Repeat the Feature-Benefit logic for every accessory.

### Phase 4: The CBBE Positioning Simulation (High Resolution)
*Ref: Workshop Part 2 - CBBE Framework. We simulate the consumer's brain.*

**Level 1: Salience (Identity)**
* **Step 11 (Category Precision):** Don't just say "Backpack." Say "Premium Board Game Transport Solution."
* **Step 12 (Problem Articulation):** State the "Bleeding Neck" problem in the simplest possible terms.
* **Step 13 (The "Grandma Test"):** Write a 1-sentence explanation that even a non-expert would understand immediately.

**Level 2: Performance (Differentiation)**
* **Step 14 (The Competitive Gap):** Contrast specifically against `[COMPETITOR_DATA]`. What exact flaw of theirs do we fix?
* **Step 15 (Points of Parity):** What "Table Stakes" features do we simply *have* to have to play the game?
* **Step 16 (Economic Value):** Justify the price point to the `[PERSONA_DATA]`. Why is $199 actually "cheap" for them?

**Level 3: Imagery (Association)**
* **Step 17 (Brand Analogy):** Complete the sentence: "We are the [FAMOUS_BRAND] of [CATEGORY]." Explain *why*.
* **Step 18 (The Usage Movie):** Describe the "Movie Scene" of usage. Lighting, location, action.
* **Step 19 (User Reflection):** When the user looks in the mirror wearing this, who do they see? (e.g., "A pro gamer," "A caring parent").

**Level 4: Judgments (Logic & Skepticism)**
* **Step 20 (The Love):** What is the first feature they will brag about to friends?
* **Step 21 (The Pre-Mortem):** What is the *primary objection* `[PERSONA_DATA]` will have? (e.g., "It's too heavy").
* **Step 22 (The Rebuttal):** Script the exact answer to that objection using technical facts.
* **Step 23 (Authority):** What gives us the right to exist? (e.g., "Founded by 3 D&D Masters").

**Level 5: Feelings (Emotion)**
* **Step 24 (Emotional Target):** Select 3 specific emotions from the persona dossier (e.g., Relief, Pride, Flow).
* **Step 25 (Tone Calibration):** Define the voice. Is it "The Wise Sage," "The Excited Best Friend," or "The Cool Expert"?

**Level 6: Resonance (Loyalty)**
* **Step 26 (The "Void" Theory):** If this product died tomorrow, what *specific experience* would be lost from the world?
* **Step 27 (Mission Lock):** Connect the Product Mission to the Persona's Identity.
* **Step 28 (The "Only" Statement):** "We are the ONLY product that [UNIQUE_CLAIM]."

### Phase 5: The Narrative Synthesis
* **Step 29 (The Positioning Summary):** Write a masterful, multi-paragraph narrative that weaves all 6 CBBE levels into a story.
* **Step 30 (Handoff Generation):** Populate the machine-readable JSON for the next agent.

## Output Instructions
Compile this massive data set into `templates/product-bible.md`. Do not leave any section blank. Be verbose. Be specific. Be persuasive.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sheshiyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
