---
name: mixing
description: > Use when this capability is needed.
metadata:
  author: thorwhalen
---

# mixing — router & core conventions

`mixing` edits audio/video from Python. This skill orients you; then jump to the
focused sub-skill for the medium you're working in.

## Pick a sub-skill

| You want to… | Sub-skill |
|---|---|
| Edit audio (slice, fade, crop, concat, overlay, normalize, align, segment) | **mixing-audio** |
| Edit video (slice, crop, loop, speed, replace/normalize audio, Ken Burns, concat, thumbnail, subtitles) | **mixing-video** |
| Transcribe speech, remove fillers, make SRT/prose, detect chapters | **mixing-transcript** |
| Re-voice / translate via text-to-speech | **mixing-dubbing** |

## First moves (every task)

1. **Check ffmpeg** — most operations need it:
   ```python
   import mixing
   assert mixing.has_ffmpeg(), "install ffmpeg (brew install ffmpeg / apt install ffmpeg)"
   ```
2. **Import lazily / specifically.** `import mixing` is cheap (no moviepy/opencv
   loaded until you touch a heavy name). Either use the facade
   (`mixing.Audio`, `mixing.Video`, `mixing.replace_audio`, …) or import the
   subpackage (`from mixing.audio import Audio`).
3. **Know the `output` convention** (below) before calling anything that writes.

## The `output` protocol (read this once, applies everywhere)

Every function that produces a result takes **one** `output` argument:

```python
crop_video("in.mp4", 5, 15, output="out.mp4")   # write a file → returns Path
crop_video("in.mp4", 5, 15, output="clips/")     # directory → auto-named file
crop_video("in.mp4", 5, 15)                       # None → saves beside input, returns Path
seg = mixing.Audio("song.mp3")[10:30]             # object producers: build first…
seg.fade_in(2).save(output="clip.mp3")            # …then save (or output=None returns Audio)
fade_in("in.mp3", output=lambda a: a.duration)    # a callable sink receives the result
```

- `None` → object producers return the in-memory object; file producers save
  beside the input and return the `Path`.
- a **file path** → write there, return `Path`.
- a **directory** → write an auto-named file inside, return `Path`.
- a **callable** → `output(result)` is returned (general escape hatch).

Functions that emit *several* artifacts qualify the destinations instead
(`remove_fillers(media, output_dir=..., output_media=...)`).

## Sliceable media

`Audio` and `Video` are lazy, sliceable views:

```python
from mixing.audio import Audio
from mixing.video import Video

Audio("song.mp3")[10:30].fade_out(3).save(output="end.mp3")   # seconds by default
Audio("song.mp3", time_unit="samples")[0:44100]               # 1s at 44.1kHz
Video("clip.mp4")[5:15].save(output="cut.mp4")
Video("clip.mp4")[100]                                         # a single frame (np BGR)
with Video("clip.mp4") as v: dur = v.duration                 # context-managed
```

## Optional extras & keys

- `pip install mixing[audio]` (pydub/soundfile), `[gen]` (Veo), `[llm]` (`aix`
  for chapter titles + SRT translation), `[widget]`, `[clipboard]`.
- ElevenLabs features (`mixing.transcript`, `mixing.dubbing`) need
  `ELEVENLABS_API_KEY` (or pass `api_key=`). Responses are cached on disk, so a
  re-run of the same input is free and offline.
- Veo (`mixing.video.genai`) needs Google Cloud auth (`GOOGLE_CLOUD_PROJECT` +
  application-default or service-account credentials).

## Gotchas

- `output=None` for file producers writes **next to the input** — pass an
  explicit `output` to control location.
- ElevenLabs/Veo calls cost money; rely on the disk cache and pass
  `cache=True` (default for these) / `refresh=True` to force a re-call.
- `mixing.video.replace_audio(..., mix_ratio=)`: `1.0` = only the new audio,
  `0.0` = keep original, `0.5` = equal blend.

---
> Source: [thorwhalen/mixing](https://github.com/thorwhalen/mixing) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
