---
name: ollama-local-setup
description: Installs Ollama, sets up local models, and deploys a custom system prompt. Invoke when user wants offline/local LLM setup or to run a model with a specific prompt. Use when this capability is needed.
metadata:
  author: tesolchina
---

# Ollama Local Setup (Mac + Windows)

Use this skill when the user asks to install a local model, avoid downloading at runtime, or deploy a system prompt for a chatbot.

## What This Skill Does

- Installs Ollama on macOS or Windows
- Adds a local model (with optional offline model copy)
- Creates a custom model with a system prompt
- Verifies the model is running

## macOS Install

1. Download the macOS installer from https://ollama.com/download/mac
2. Drag Ollama.app into Applications
3. Launch Ollama once to allow it to add the CLI link
4. Verify in Terminal:

```
ollama --version
```

## Windows Install

1. Download the Windows installer from https://ollama.com/download
2. Run the installer and finish setup
3. Open PowerShell and verify:

```
ollama --version
```

## Add Model (Online Pull)

```
ollama pull llama3.2:1b
```

## Add Model (Offline Copy)

If you already have the model files, copy them into the Ollama model directory.

macOS:

- Copy the `models` folder into:
  - `~/.ollama/models`

Windows:

- Copy the `models` folder into:
  - `%USERPROFILE%\.ollama\models`

Then verify:

```
ollama list
```

## Create Custom Model With System Prompt

1. Create a file named `Modelfile`:

```
FROM llama3.2:1b
SYSTEM """
You are an academic presentation coach for PhD students. Help them craft clear, rigorous, and engaging research talks.

Focus on:
1. Core contribution and novelty
2. Research question, methods, and evidence
3. Slide structure and narrative flow
4. Concise wording and visual clarity
5. Anticipating questions and limitations

Ask one focused question at a time. Keep responses concise, actionable, and tailored to the student's field and audience.
"""
```

2. Create the model:

```
ollama create phd-presentation-coach -f Modelfile
```

3. Run it:

```
ollama run phd-presentation-coach
```

## Run the API Server

```
ollama serve
```

API endpoint:

- `http://localhost:11434/api/chat`

## Quick Test Prompt

```
ollama run phd-presentation-coach "ok"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tesolchina) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
