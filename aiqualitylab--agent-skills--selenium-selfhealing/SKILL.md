---
name: selenium-self-healing
description: AI-powered self-healing Selenium tests with Reqnroll BDD Use when this capability is needed.
metadata:
  author: aiqualitylab
---

# Selenium Self-Healing Skill

## Overview
Create Selenium tests that automatically recover from broken locators using local AI (Ollama).

## Capabilities
- Auto-heal broken locators using AI analysis
- BDD testing with Reqnroll (Gherkin syntax)
- Works with free local AI (Ollama) or GPT
- .NET 9 + Selenium WebDriver + NUnit

## Usage

### Generate Self-Healing Test
```
Create a Selenium test for login page that:
- Navigates to the login URL
- Enters credentials
- Clicks submit
- Uses self-healing locators
```

### Fix Broken Locator
```
The locator By.Id("searchBox") is failing.
Analyze the page and suggest alternative locators.
```

## Quick Start
```bash
# 1. Install Ollama AI
ollama pull qwen3-coder:480b-cloud

# 2. Clone and run
git clone https://github.com/aiqualitylab/SeleniumSelfHealing.Reqnroll.git
cd SeleniumSelfHealing.Reqnroll.Net9
dotnet test
```

## Installation
```bash
skills install aiqualitylab/selenium-selfhealing
```

## Links
- [GitHub Repository](https://github.com/aiqualitylab/SeleniumSelfHealing.Reqnroll)
- [Author](https://aiqualityengineer.com)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiqualitylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
