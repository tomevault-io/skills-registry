---
name: elevenlabs
description: Use for generating speech, voiceovers, and audio content with ElevenLabs. Triggers on "text to speech", "generate audio", "voiceover", "clone voice", "transcribe audio", "sound effects", "ElevenLabs", or when creating audio for courses, videos, or podcasts. Leverages ElevenLabs MCP server for direct integration. Use when this capability is needed.
metadata:
  author: webmasterarbez
---

# Generating Audio with ElevenLabs

This skill enables AI-powered audio generation using ElevenLabs' text-to-speech, voice cloning, transcription, and sound effects capabilities. It integrates via the official ElevenLabs MCP server for seamless workflow.

## MCP Server Integration

The ElevenLabs MCP server provides direct access to all ElevenLabs capabilities. When properly configured, Claude can generate speech, clone voices, and process audio through natural language commands.

### Verifying MCP Server Availability

Check if the ElevenLabs MCP server is configured by looking for available tools. If not available, guide the user through setup (see `./mcp-server-setup.md`).

### Available MCP Tools

When the ElevenLabs MCP server is active, these tools become available:

| Tool | Purpose |
|------|---------|
| `text_to_speech` | Convert text to natural speech |
| `get_voices` | List available voices |
| `voice_clone` | Clone a voice from audio samples |
| `transcribe` | Convert audio/video to text |
| `sound_effects` | Generate sound effects from descriptions |
| `voice_isolate` | Separate speech from background noise |
| `audio_convert` | Apply voice effects to audio |

## Core Workflows

### 1. Text-to-Speech Generation

Generate voiceovers for course content, videos, or podcasts:

```
Generate speech for: "Welcome to Module 1. In this lesson, we'll explore..."
Voice: Use a warm, professional voice
Model: eleven_multilingual_v2 (for stability) or eleven_flash_v2_5 (for speed)
```

**Model Selection Guide:**
- **Eleven v3 (Alpha)**: Most expressive, dramatic delivery, 70+ languages, 5K char limit
- **Eleven Multilingual v2**: Most stable for longer content, 29 languages, 10K char limit
- **Eleven Flash v2.5**: Ultra-low latency (~75ms), 32 languages, 40K char limit
- **Eleven Turbo v2.5**: Balance of quality/speed (250-300ms), 32 languages, 40K char limit

### 2. Voice Cloning

Create custom voices from audio samples:

1. Prepare 1-3 minutes of clean audio (no background noise)
2. Use `voice_clone` tool with the audio file path
3. Name the voice descriptively (e.g., "course-narrator-professional")

### 3. Audio Transcription

Transcribe recordings using Scribe models:

```
Transcribe the audio file at: ./recordings/interview.mp3
Include speaker diarization (up to 32 speakers)
```

**Scribe Models:**
- **Scribe v1**: 99 languages, speaker diarization
- **Scribe v2 Realtime**: 90 languages, ~150ms latency

### 4. Sound Effects Generation

Create custom sound effects for videos:

```
Generate sound effect: "gentle notification chime"
Generate sound effect: "applause from small audience"
```

## Course Audio Production Workflow

For producing course audio content:

### Step 1: Prepare Scripts
Ensure scripts are finalized and saved as text files. See `./voice-generation-workflows.md` for script formatting tips.

### Step 2: Select Voice
```
List available voices with get_voices
Choose based on:
- Gender and age range
- Accent and language
- Tone (professional, casual, energetic)
```

### Step 3: Generate Audio
```
For each lesson script:
1. Generate speech with selected voice
2. Save to content/audio/module-XX/lesson-XX.mp3
3. Verify audio quality
```

### Step 4: Post-Processing
- Use `voice_isolate` to clean any recordings with background noise
- Apply consistent audio levels across all files

## Output File Organization

```
content/
├── audio/
│   ├── module-00/
│   │   ├── lesson-01-intro.mp3
│   │   └── lesson-02-overview.mp3
│   ├── module-01/
│   │   └── ...
│   └── sound-effects/
│       ├── transition-chime.mp3
│       └── success-notification.mp3
└── transcripts/
    └── ...
```

## Best Practices

### For Voiceovers
- Keep segments under 5,000 characters for v3, under 10,000 for Multilingual v2
- Add natural pauses with `...` or line breaks
- Test voice with a short sample before full generation
- Use consistent voice across a course for professional feel

### For Voice Cloning
- Use high-quality source audio (no compression artifacts)
- Provide diverse samples (different emotions, pacing)
- Label cloned voices clearly in your project

### For Transcription
- Enable speaker diarization for interviews/dialogues
- Request timestamps for video synchronization
- Review and correct specialized terminology

## Troubleshooting

| Issue | Solution |
|-------|----------|
| MCP server not available | See `./mcp-server-setup.md` for configuration |
| Audio quality issues | Try different model or voice settings |
| Timeout on large files | Break content into smaller segments |
| Voice sounds unnatural | Adjust text formatting, add punctuation for pacing |

## API Credits

ElevenLabs uses a credit-based system:
- Free tier: 10,000 characters/month
- Flash/Turbo models: 50% lower cost per character
- Monitor usage at elevenlabs.io dashboard

## References

- `./mcp-server-setup.md` - Complete MCP server configuration guide
- `./voice-generation-workflows.md` - Detailed workflows for different use cases
- [ElevenLabs Documentation](https://elevenlabs.io/docs/overview/intro)
- [ElevenLabs MCP GitHub](https://github.com/elevenlabs/elevenlabs-mcp)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
