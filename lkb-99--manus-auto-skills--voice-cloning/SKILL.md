---
name: voice-cloning
description: Comprehensive voice cloning and text-to-speech (TTS) synthesis. Use this skill when users want to clone a voice, generate speech from text, create voiceovers, or use TTS APIs like ElevenLabs. Triggers: voice cloning, clonagem de voz, text-to-speech, TTS, speech synthesis, generate audio, voiceover, ElevenLabs, Coqui, narrar, narração, locução, sintetizar voz. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Voice Cloning and Speech Synthesis

## Overview
This skill provides a comprehensive toolkit for voice cloning and text-to-speech (TTS) synthesis. It enables the agent to generate high-quality audio in various voices, clone existing voices from audio samples, and integrate these capabilities into complex workflows. The skill primarily leverages the ElevenLabs and Coqui TTS APIs, offering a wide range of options for voice generation, from instant voice cloning with a few seconds of audio to professional-grade cloning for creating highly realistic and versatile voice models.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: voice cloning, clonagem de voz, text-to-speech, TTS, speech synthesis, generate audio, voiceover, ElevenLabs, Coqui, narrar, narração, locução, sintetizar voz, criar áudio, gerar áudio, voz sintética.
- Phrases: "clone my voice", "clonar minha voz", "create a voiceover", "criar uma locução", "text to speech", "texto em áudio", "sintetizar um texto", "gerar áudio de um texto".
- Context: Any discussion about generating audio from text, creating synthetic voices, or using voice cloning technologies.

**Example user queries that trigger this skill:**
- "Can you clone a voice from an audio file?"
- "Quero transformar este artigo em um podcast."
- "Preciso de uma narração para meu vídeo."
- "How do I use the ElevenLabs API for text-to-speech?"

## When to Use This Skill
This skill is particularly useful in a variety of scenarios that require the generation of spoken audio. It can be used to:

*   **Create audio versions of text content:** Convert articles, blog posts, or any written material into audio format for podcasts, accessibility, or other applications.
*   **Generate voiceovers for videos:** Create professional-sounding voiceovers for marketing videos, educational content, or presentations.
*   **Develop interactive voice response (IVR) systems:** Build custom voice prompts and responses for automated phone systems.
*   **Personalize user experiences:** Generate audio messages in a specific voice to create a more engaging and personalized interaction.
*   **Clone a user's voice:** Create a digital replica of a user's voice for various applications, such as personalized assistants or content creation.
*   **Prototype voice-based applications:** Quickly prototype and test voice-based applications without the need for professional voice actors.
*   **Translate and dub audio content:** Generate speech in different languages while maintaining the characteristics of the original speaker's voice.

## Core Capabilities

### 1. Text-to-Speech (TTS) Synthesis
The core of this skill is its ability to convert text into natural-sounding speech. This is achieved through the use of advanced TTS models that can generate audio with realistic intonation, pacing, and emotional expression.

*   **Multiple Voice Options:** Access a wide range of pre-existing voices from the ElevenLabs and Coqui TTS libraries.
*   **Customizable Speech Parameters:** Control various aspects of the generated speech, such as speed, pitch, and volume.
*   **Support for Multiple Languages:** Generate speech in numerous languages, making it suitable for global applications.

### 2. Instant Voice Cloning (IVC)
Instantly clone a voice from a short audio sample (as little as 6 seconds). This feature is ideal for quickly creating a voice clone for immediate use.

*   **Requires Minimal Audio Data:** A small audio sample is sufficient to create a functional voice clone.
*   **Fast Processing:** The cloning process is quick, allowing for rapid prototyping and iteration.
*   **Good for General-Purpose Cloning:** Suitable for applications where a high degree of fidelity is not the primary concern.

### 3. Professional Voice Cloning (PVC)
For applications requiring the highest level of accuracy and naturalness, Professional Voice Cloning is the recommended approach. This method involves a more detailed process of training a custom voice model.

*   **High-Fidelity Voice Replication:** Creates a highly realistic and indistinguishable digital replica of the original voice.
*   **Requires More Training Data:** A larger dataset of high-quality audio is needed to train the model.
*   **Ideal for Commercial and Professional Use:** Best suited for applications where voice quality is paramount.

### 4. API Integration
The skill is designed to seamlessly integrate with the ElevenLabs and Coqui TTS APIs. This allows for programmatic control over all the voice cloning and speech synthesis functionalities.

*   **Python SDKs:** Utilize the official Python SDKs for both ElevenLabs and Coqui TTS for easy integration into Python-based workflows.
*   **REST APIs:** Directly interact with the REST APIs for more granular control and integration with other programming languages.
*   **Secure API Key Management:** The skill can securely manage API keys, ensuring that they are not exposed in the code.

## Step-by-Step Workflow

### 1. Setup and Configuration
Before using the skill, you need to set up the necessary API keys and install the required libraries.

**a. Obtain API Keys:**
*   **ElevenLabs:** Sign up for an account at [https://elevenlabs.io/](https://elevenlabs.io/) and get your API key from the dashboard.
*   **Coqui TTS:** Coqui TTS is an open-source library, so you don't need an API key for local use. However, if you are using a hosted version, you will need to obtain the necessary credentials.

**b. Install Libraries:**
Use pip to install the required Python libraries:

```bash
sudo pip3 install elevenlabs-python coqui-tts
```

**c. Set Environment Variables:**
For security, it's best to store your API keys as environment variables.

```bash
export ELEVENLABS_API_KEY='your_elevenlabs_api_key'
```

### 2. Text-to-Speech with ElevenLabs

**a. Initialize the Client:**

```python
from elevenlabs import set_api_key, generate, play

set_api_key(os.environ.get("ELEVENLABS_API_KEY"))
```

**b. Generate Speech:**

```python
audio = generate(
    text="Hello! This is a test of the ElevenLabs text-to-speech API.",
    voice="Bella",
    model="eleven_multilingual_v2"
)
```

**c. Play or Save the Audio:**

```python
play(audio)

# To save to a file:
with open("output.mp3", "wb") as f:
    f.write(audio)
```

### 3. Instant Voice Cloning with ElevenLabs

**a. Prepare Audio Sample:**
You will need one or more audio files of the voice you want to clone. The files should be in a common format like MP3 or WAV.

**b. Clone the Voice:**

```python
from elevenlabs import clone, generate, play

voice = clone(
    name="My Cloned Voice",
    description="A clone of my own voice.",
    files=["/path/to/audio1.mp3", "/path/to/audio2.mp3"]
)
```

**c. Generate Speech with the Cloned Voice:**

```python
audio = generate(text="This is a test of my cloned voice.", voice=voice)
play(audio)
```

### 4. Text-to-Speech with Coqui TTS (Local)

**a. Download a Pre-trained Model:**
Coqui TTS provides a variety of pre-trained models. You can list them using the `tts` command-line tool.

```bash
tts --list_models
```

**b. Synthesize Speech:**

```bash
tts --text "This is a test of Coqui TTS." --model_name "tts_models/en/ljspeech/tacotron2-DDC" --out_path output.wav
```

**c. Use in Python:**

```python
import torch
from TTS.api import TTS

# Get device
device = "cuda" if torch.cuda.is_available() else "cpu"

# Init TTS
tts = TTS("tts_models/en/ljspeech/tacotron2-DDC").to(device)

# Run TTS
# ❗ Slower than training process.
# text_to_speech(text="Hello world!", speaker=None, language=None, speed=1.0)
tts.tts_to_file(text="Hello world!", file_path="output.wav")
```

### 5. Voice Cloning with Coqui TTS

**a. Prepare Your Dataset:**
For high-quality voice cloning with Coqui TTS, you will need a dataset of audio files and a corresponding transcript. The dataset should be formatted in a specific way, typically with a `metadata.csv` file that maps audio file paths to their transcriptions.

**b. Train a New Model:**
Training a new model is a more involved process that requires a significant amount of data and computational resources. The Coqui TTS documentation provides detailed instructions on how to train your own models.

## Best Practices

*   **Use High-Quality Audio for Cloning:** The quality of the cloned voice is directly dependent on the quality of the input audio. Use clear, noise-free recordings for the best results.
*   **Choose the Right Model:** Select the TTS model that best suits your needs in terms of language, voice style, and performance.
*   **Manage API Keys Securely:** Never hardcode your API keys in your scripts. Use environment variables or a secure key management system.
*   **Handle Errors Gracefully:** Implement error handling in your code to manage potential issues with API requests or audio generation.
*   **Respect Privacy and Copyright:** Only use voices that you have the right to clone. Be mindful of privacy and copyright laws.
*   **Experiment with Different Parameters:** Tweak the speech parameters (speed, pitch, etc.) to achieve the desired output.

## Examples

### Example 1: Creating a Podcast Intro

```python
from elevenlabs import generate, play

intro_text = """
Welcome to the AI Today podcast, where we explore the latest advancements in artificial intelligence and machine learning. In today's episode, we'll be discussing the future of voice synthesis.
"""

audio = generate(
    text=intro_text,
    voice="Adam",
    model="eleven_multilingual_v2"
)

play(audio)
```

### Example 2: Generating a Personalized Greeting

```python
from elevenlabs import clone, generate

def generate_greeting(name, audio_files):
    voice = clone(
        name=f"{name}'s Voice",
        files=audio_files
    )

    greeting_text = f"Hello {name}, welcome back! I hope you're having a great day."
    audio = generate(text=greeting_text, voice=voice)
    return audio

# Example usage:
# user_name = "Alice"
# user_audio_samples = ["/path/to/alice1.mp3", "/path/to/alice2.mp3"]
# greeting_audio = generate_greeting(user_name, user_audio_samples)
# play(greeting_audio)
```

### Example 3: Using Coqui TTS for Offline Speech Synthesis

```python
import torch
from TTS.api import TTS

def synthesize_offline(text, output_path):
    device = "cuda" if torch.cuda.is_available() else "cpu"
    tts = TTS("tts_models/en/ljspeech/tacotron2-DDC").to(device)
    tts.tts_to_file(text=text, file_path=output_path)

# Example usage:
# offline_text = "This is an example of offline speech synthesis with Coqui TTS."
# synthesize_offline(offline_text, "offline_output.wav")
```

## References

*   **ElevenLabs API Documentation:** [https://elevenlabs.io/docs/api-reference/introduction](https://elevenlabs.io/docs/api-reference/introduction)
*   **ElevenLabs Python SDK:** [https://github.com/elevenlabs/elevenlabs-python](https://github.com/elevenlabs/elevenlabs-python)
*   **Coqui TTS Documentation:** [https://coqui-tts.readthedocs.io/](https://coqui-tts.readthedocs.io/)
*   **Coqui TTS GitHub Repository:** [https://github.com/coqui-ai/TTS](https://github.com/coqui-ai/TTS)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
