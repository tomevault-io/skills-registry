---
name: yolo-detection-2026-coral-tpu-win-wsl
description: Real-time object detection on live camera frames via Edge TPU inside WSL Use when this capability is needed.
metadata:
  author: SharpAI
---

# Coral TPU Object Detection (Windows WSL)

Real-time object detection natively utilizing the Google Coral Edge TPU accelerator on your local hardware via Windows Subsystem for Linux (WSL). Detects 80 COCO classes (person, car, dog, cat, etc.) with ~4ms inference on 320x320 input.

## Requirements

- **Google Coral USB Accelerator** (USB 3.0 port recommended)
- **WSL2** installed and running on Windows
- `usbipd-win` installed on the Windows host

## How It Works

```
┌─────────────────────────────────────────────────────┐
│ Host (Aegis-AI on Windows)                          │
│   frame.jpg → /tmp/aegis_detection/                 │
│   stdin  ──→ ┌──────────────────────────────┐       │
│              │ WSL Container / Environment   │       │
│              │   detect.py                   │       │
│              │   ├─ loads _edgetpu.tflite     │       │
│              │   ├─ reads frame from disk     │       │
│              │   └─ runs inference on TPU    │       │
│   stdout ←── │   → JSONL detections          │       │
│              └──────────────────────────────┘       │
│   USB ──→ usbipd-win bridge to WSL                  │
└─────────────────────────────────────────────────────┘
```

1. Aegis writes camera frame JPEG to shared `/tmp/aegis_detection/` workspace
2. Sends `frame` event via stdin JSONL to the WSL Python instance
3. `detect.py` invokes PyCoral and executes natively on the mapped USB Edge TPU inside Linux
4. Returns `detections` event via stdout JSONL back to Windows Host

## Performance

| Input Size | Inference | On-chip | Notes |
|-----------|-----------|---------|-------|
| 320x320 | ~4ms | 100% | Fully on TPU, best for real-time |
| 640x640 | ~20ms | Partial | Some layers on CPU (model segmented) |

> **Cooling**: The USB Accelerator aluminum case acts as a heatsink. If too hot to touch during continuous inference, it will thermal-throttle. Consider active cooling or `clock_speed: standard`.

## Installation

### Windows (WSL)
Run `deploy.bat` — this will:
1. Verify `usbipd` is installed and bind the `18d1:9302` and `1a6e:089a` Edge TPU hardware IDs.
2. Setup a Python virtual environment exclusively within WSL.
3. Install the Edge TPU libraries and dependencies within the WSL boundary.
4. Auto-attach the device using `usbipd` seamlessly during invocation.

---
> Source: [SharpAI/DeepCamera](https://github.com/SharpAI/DeepCamera) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
