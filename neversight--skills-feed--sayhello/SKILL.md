---
name: sayhello
description: Generate friendly greetings in multiple languages and styles. Use this skill when users request greetings, welcome messages, or multilingual hello messages for various contexts (formal, casual, cultural). Use when this capability is needed.
metadata:
  author: neversight
---

# Say Hello Skill

A skill that helps generate friendly, contextual greetings in multiple languages and styles.

## Overview

Generate personalized greetings for various contexts including:
- Multilingual greetings (English, Chinese, Japanese, Spanish, French, etc.)
- Different formality levels (formal, casual, professional)
- Cultural contexts (business meetings, social gatherings, emails)
- Time-based greetings (morning, afternoon, evening)
- Custom greeting templates

## Quick Start

### Basic Usage

Generate a simple greeting:
```
"Say hello in Chinese"
→ 你好！(Nǐ hǎo!)
```

Generate a formal greeting:
```
"Generate a formal business greeting for a Japanese client"
→ おはようございます。お会いできて光栄です。(Ohayō gozaimasu. O-ai dekite kōei desu.)
```

Generate time-based greeting:
```
"Create a good morning greeting"
→ Good morning! Hope you have a wonderful day ahead!
```

## Core Capabilities

### 1. Multilingual Greetings

Generate greetings in various languages with proper pronunciation guides:

**Supported Languages:**
- English
- 中文 (Chinese - Simplified & Traditional)
- 日本語 (Japanese)
- Español (Spanish)
- Français (French)
- Deutsch (German)
- 한국어 (Korean)
- Italiano (Italian)
- Português (Portuguese)
- Русский (Russian)

**Example:**
```
"Say hello in Spanish with pronunciation"
→ ¡Hola! (OH-lah)
   ¡Buenos días! (BWEH-nos DEE-ahs)
```

### 2. Context-Aware Greetings

Generate greetings appropriate for different contexts:

**Business Context:**
```
"Professional greeting for email"
→ Dear [Name],
   I hope this message finds you well.
```

**Casual Context:**
```
"Casual greeting for friends"
→ Hey there! What's up?
```

**Cultural Events:**
```
"Chinese New Year greeting"
→ 新年快乐！恭喜发财！
   (Xīn nián kuài lè! Gōng xǐ fā cái!)
   Happy New Year! Wishing you prosperity!
```

### 3. Time-Based Greetings

Generate appropriate greetings based on time of day:

- **Morning:** "Good morning", "早上好"
- **Afternoon:** "Good afternoon", "下午好"
- **Evening:** "Good evening", "晚上好"
- **Night:** "Good night", "晚安"

### 4. Custom Templates

Use the greeting templates in `assets/` for consistent, branded greetings:

**Email Templates:**
- Formal business introduction
- Casual team communication
- Client outreach
- Meeting invitations

**Social Templates:**
- Birthday greetings
- Holiday greetings
- Welcome messages
- Thank you notes

## Using Scripts

### greet.py

Generate greetings programmatically using the included Python script:

```bash
python scripts/greet.py --language chinese --style formal
```

**Parameters:**
- `--language`: Target language (default: english)
- `--style`: Formality level (formal/casual/professional)
- `--context`: Specific context (business/social/email)
- `--time`: Time of day (morning/afternoon/evening/night)

**Example:**
```bash
# Generate formal Chinese business greeting
python scripts/greet.py --language chinese --style formal --context business

# Generate casual morning greeting
python scripts/greet.py --style casual --time morning
```

## Reference Materials

For comprehensive greeting conventions across cultures, pronunciation guides, and cultural etiquette, refer to:

- `reference/greetings.md` - Complete greeting reference for all supported languages
- `reference/cultural_contexts.md` - Cultural considerations and etiquette guidelines

## Best Practices

1. **Consider Cultural Context:** Always consider the recipient's cultural background when choosing greetings
2. **Match Formality Level:** Ensure the greeting matches the relationship and context
3. **Include Pronunciation:** When using non-Latin scripts, include pronunciation guides
4. **Time Awareness:** Use time-appropriate greetings when context is known
5. **Personalization:** Customize greetings with names and specific details when available

## Examples

### Scenario 1: International Team Email
```
Request: "Greeting for international team email"

Response:
Hello Team / 大家好 / みなさん、こんにちは

I hope this message finds you all well. I wanted to reach out regarding...
```

### Scenario 2: Formal Business Introduction
```
Request: "Formal greeting for new Chinese business partner"

Response:
尊敬的 [Partner Name]，

非常荣幸能与贵公司合作。期待我们之间建立长期互利的合作关系。

(Zūn jìng de [Partner Name],
Fēi cháng róng xìng néng yǔ guì gōng sī hé zuò. Qī dài wǒ men zhī jiān jiàn lì cháng qī hù lì de hé zuò guān xì.)

Dear [Partner Name],
It is a great honor to collaborate with your esteemed company. We look forward to establishing a long-term mutually beneficial partnership.
```

### Scenario 3: Casual Social Greeting
```
Request: "Fun greeting for team Slack channel"

Response:
Hey everyone! 👋
Hope you're all having an awesome day! ☀️
```

## Advanced Features

### Greeting Chains
Generate greeting sequences for multi-step interactions:
1. Initial greeting
2. Introduction
3. Purpose statement
4. Closing

### Cultural Adaptations
Automatically adapt greetings based on:
- Regional dialects
- Cultural holidays
- Business customs
- Social norms

### A/B Testing Support
Generate multiple greeting variations for testing effectiveness in different contexts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
