---
name: m5stack-development
description: > Use when this capability is needed.
metadata:
  author: serkanyersen
---

# M5Stack Development with PlatformIO

Guide for developing firmware for M5Stack devices using PlatformIO,
Arduino framework, and M5Unified library.

## Supported Devices

The primary device documented is the **M5StickS3**, but M5Unified
auto-detects the connected M5Stack device at runtime. The same codebase
works across the M5Stack family with minimal changes.

For detailed hardware specs and pinouts, consult
**`references/m5sticks3-hardware.md`**.

## Project Setup

### Initialize a PlatformIO Project

```bash
pio project init --board esp32-s3-devkitc-1 --project-option "framework=arduino"
```

### Configure platformio.ini

A working example configuration is available at **`examples/platformio.ini`**.

Key configuration points for ESP32-S3 based M5Stack devices:

- **Board**: `esp32-s3-devkitc-1` (no dedicated M5StickS3 board in PlatformIO yet)
- **Memory type**: `qio_opi` for the N8R8 module (8MB Flash + 8MB PSRAM)
- **USB CDC**: Required for serial output over native USB — set `ARDUINO_USB_CDC_ON_BOOT=1`
- **PSRAM**: Enable with `-DBOARD_HAS_PSRAM` build flag
- **Library**: `m5stack/M5Unified` (auto-detects device, includes M5GFX)

### Identifying the Serial Port

ESP32-S3 native USB appears as `/dev/cu.usbmodem*` on macOS (not `/dev/cu.usbserial*`).
Detect with:

```bash
ls /dev/cu.usb*
```

## Upload Workflow

ESP32-S3 with native USB requires **manual boot mode entry** before uploading:

1. **Hold the Boot button** (labeled G0 or BOOT on the device side)
2. While holding Boot, **press and release Reset** (RST button)
3. **Release Boot** — screen goes blank, device is in download mode
4. Run `pio run -t upload`
5. Device auto-resets after successful upload

**Once firmware with USB CDC is running**, subsequent uploads work automatically
without manual boot mode — the upload tool resets the device via DTR/RTS signals.
Manual boot mode is only needed for:
- The very first flash onto a blank/factory device
- Recovery after a firmware crash that prevents USB CDC from initializing

## M5Unified Library Patterns

### Initialization

```cpp
#include <M5Unified.h>

void setup() {
    auto cfg = M5.config();
    cfg.internal_imu = true;   // Enable IMU
    M5.begin(cfg);

    M5.Display.setRotation(1); // Landscape: 240x135
}

void loop() {
    M5.update(); // Must call every loop — updates buttons, IMU, touch
}
```

### Display (M5Canvas Double-Buffering)

For flicker-free drawing, use an off-screen sprite:

```cpp
static M5Canvas canvas(&M5.Display);

void setup() {
    // ... after M5.begin()
    canvas.createSprite(240, 135);  // Full screen landscape
}

void loop() {
    canvas.fillSprite(TFT_BLACK);   // Clear
    canvas.fillCircle(120, 67, 10, TFT_YELLOW);
    canvas.pushSprite(0, 0);        // Blit to screen
}
```

Display in landscape rotation 1: **240 x 135 pixels** (width x height).

### IMU (Accelerometer + Gyroscope)

**Critical**: Call `M5.Imu.update()` explicitly each loop iteration before
reading data. Without this, `getImuData()` returns all zeros on StickS3.

```cpp
M5.Imu.update();  // Must call explicitly — M5.update() alone is NOT enough
auto imu = M5.Imu.getImuData();
float ax = imu.accel.x;  // Acceleration in g (-1.0 to 1.0)
float ay = imu.accel.y;
float az = imu.accel.z;
float gx = imu.gyro.x;   // Angular velocity in dps
```

IMU axis mapping in landscape rotation 1 (240x135):

- `accel.x` maps to screen X axis (negate for natural "gravity" tilt feel)
- `accel.y` maps to screen Y axis

Consult **`references/m5sticks3-hardware.md`** for axis orientation diagrams.

### Device Orientation (Landscape Rotation 1)

```
             B (side button, top edge) (MIC)
       ┌────────────────=====─────────.─┐
 IR-TX │ ┌─────────────────────┐        │
       │ │                     │  ┌┐    │
  SPK  │ │ ▲Y                  │  ││ A  = USB
       │ │ │                   │  ││    =
       │ │ └───►X              │  └┘    │
 IR-RX │ └─────────────────────┘        │
       └───────────────────────────===──┘
                               power
```

- **A button**: Right side of screen (front face)
- **B button**: Top edge (side button)
- **USB**: Right edge
- **Screen**: 240x135, origin top-left, X→right, Y→down
- **IMU**: negate `accel.x` for natural gravity on screen X axis

### IMU Best Practices

**EMA low-pass filter** to remove sensor jitter (alpha=0.25 is a good balance):

```cpp
static float axSmooth = 0, aySmooth = 0;

M5.Imu.update();
auto imu = M5.Imu.getImuData();
float axRaw = constrain(-imu.accel.x, -1.0f, 1.0f);  // Clamp to ±1g
float ayRaw = constrain(imu.accel.y, -1.0f, 1.0f);
axSmooth += 0.25f * (axRaw - axSmooth);  // EMA: ~4 frame smoothing
aySmooth += 0.25f * (ayRaw - aySmooth);
```

- **Clamp to ±1g** prevents extreme force spikes during shaking
- **EMA alpha=0.25** removes jitter without feeling sluggish (0.08 is too laggy)
- Without smoothing, beads/balls "boil" in corners from sensor noise

### Buttons

```cpp
M5.update();
if (M5.BtnA.wasPressed())   { /* Front button — single press */ }
if (M5.BtnA.isPressed())    { /* Front button — held down */ }
if (M5.BtnA.wasReleased())  { /* Front button — just released */ }
if (M5.BtnB.wasPressed())   { /* Side button */ }
```

**Short press vs hold pattern** (e.g., tap=action1, hold=action2):

```cpp
static bool held = false;
static unsigned long downTime = 0;

if (M5.BtnA.wasPressed()) { downTime = millis(); held = false; }
if (M5.BtnA.isPressed() && millis() - downTime > 300) {
    held = true;
    // Hold action here (runs every frame while held)
}
if (M5.BtnA.wasReleased() && !held) {
    // Short press action here
}
```

### Speaker

```cpp
M5.Speaker.tone(880, 100);   // frequency Hz, duration ms
M5.Speaker.setVolume(128);   // 0-255
```

**Raw PCM playback** for sound effects (clicks, impacts — much better than `tone()`):

```cpp
// Generate noise-burst click sample at init
static int16_t click[200];
for (int i = 0; i < 200; i++) {
    float noise = ((float)((int32_t)(esp_random() >> 1)) / (float)(INT32_MAX / 2));
    float envelope = expf(-(float)i / 200 * 5.0f);
    click[i] = (int16_t)(noise * envelope * 30000.0f);
}

// Play on collision (channel 0, 16kHz, stop_current=true)
M5.Speaker.setChannelVolume(0, volume);  // 0-255
M5.Speaker.playRaw(click, 200, 16000, false, 1, 0, true);
```

### Microphone

```cpp
auto micCfg = M5.Mic.config();
micCfg.sample_rate = 16000;
micCfg.magnification = 4;
M5.Mic.config(micCfg);
M5.Mic.begin();
int16_t buf[256];
M5.Mic.record(buf, 256, 16000);
```

**Critical**: On StickS3, the mic and speaker share the ES8311 audio codec.
When switching from mic to speaker, fully reinitialize:

```cpp
M5.Mic.end();
M5.Speaker.end();
delay(50);
M5.Speaker.begin();
delay(50);
// Speaker now works cleanly — without this, feedback/screeching occurs
```

### Power Management

```cpp
M5.Power.getBatteryLevel();   // 0-100%
M5.Power.isCharging();        // true if USB power
```

## Build and Monitor Commands

```bash
pio run                    # Build only
pio run -t upload          # Build and upload
pio device monitor         # Serial monitor (115200 baud)
pio run -t upload && pio device monitor  # Upload then monitor
```

## Troubleshooting

| Problem                    | Solution                                                                                        |
| -------------------------- | ----------------------------------------------------------------------------------------------- |
| "Could not configure port" | Device not in download mode. boot first, then hold boot 2 seconds until you see flashing light. |
| No serial output           | Ensure `-DARDUINO_USB_CDC_ON_BOOT=1` in build flags                                             |
| Display blank after upload | Check `M5.Display.setRotation()` value                                                          |
| IMU returns zeros          | Call `M5.Imu.update()` explicitly each loop — required on StickS3                               |
| Library not found          | Run `pio lib install "m5stack/M5Unified"`                                                       |
| PSRAM not detected         | Add `-DBOARD_HAS_PSRAM` and set `memory_type = qio_opi`                                         |

## Additional Resources

### Reference Files

- **`references/m5sticks3-hardware.md`** — Detailed hardware specs, chip info, peripheral details, and pin assignments for the M5StickS3
- **`references/platformio-troubleshooting.md`** — Extended troubleshooting, ESP32-S3 USB quirks, partition schemes, and build flag reference

### Example Files

- **`examples/platformio.ini`** — Working PlatformIO configuration for M5StickS3

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/serkanyersen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
