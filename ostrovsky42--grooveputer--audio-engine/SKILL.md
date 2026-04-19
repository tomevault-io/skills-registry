---
name: audio-engine
description: Technical guidelines and constraints for the MiniAcid audio engine on ESP32-S3. Use when this capability is needed.
metadata:
  author: ostrovsky42
---

# Audio Engine Skill

This skill provides essential guidelines for working on the high-performance DSP engine of MiniAcid, optimized for the M5Stack Cardputer.

## 🚀 Core Principles
- **Predictable Performance:** Every cycle counts. Avoid heavy math in the main callback loop.
- **Thread Safety:** The audio callback runs on **Core 1**, while the UI/Logic runs on **Core 0**.
- **Real-time Safety:** NO memory allocation (`malloc`, `new`) or I/O (`Serial`, `SD`) inside the audio callback.

## 🛠 DSP Specifications
- **Sample Rate:** 
  - **ESP32:** 22050 Hz (balanced for performance/quality).
  - **Desktop/SDL:** 44100 Hz.
- **Normalization:** Internal parameters MUST use `0.0f` to `1.0f`.
- **Soft Clamping:** Every voice should include a soft limiter/clipper to prevent harsh digital wrapping.

## 🧵 Thread Synchronization
- Use `std::atomic` for simple flag/param updates.
- Use `AudioGuard` (mutex-like wrapper) when modifying complex synth state from the UI thread.
- **Waveform Buffer:** Access visual data via `mini_acid_.getWaveformBuffer()` which uses atomic double-buffering.

## ⚠️ Critial Constraints
- **Zero PSRAM:** The Cardputer has NO PSRAM. Use `DRAM` sparingly.
- **Fixed-Point vs Float:** While ESP32-S3 has an FPU, prefer optimized fixed-point or simple float math. Avoid `double` entirely.

## 📁 Key Files
- `src/dsp/`: Core DSP implementations.
- `src/voice_cache.h`: Voice management and pooling.
- `src/miniacid.cpp`: Main audio callback and routing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ostrovsky42) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
