---
name: hand-drawing-testing
description: Toolkit for testing hand drawing applications including signature capture, canvas drawing, and art apps. Supports touch gesture simulation for drawing strokes, image comparison for output verification, and multi-stroke automation. Use when testing drawing/canvas features, signature capture, annotation tools, or any app requiring touch path validation. Use when this capability is needed.
metadata:
  author: hungson175
---

# Hand Drawing App Testing

Test hand drawing applications using touch simulation and image comparison.

**Helper Scripts Available**:
- `scripts/draw_stroke.py` - Simulate touch drawing strokes via ADB
- `scripts/compare_canvas.py` - Compare canvas screenshots against baselines
- `scripts/capture_drawing.py` - Capture and save drawing area screenshots

**Always run scripts with `--help` first** to see usage. DO NOT read the source until you try running the script first and find that a customized solution is absolutely necessary.

## Decision Tree: Choosing Your Approach

```
User task -> What type of drawing test?
    |
    |-- Simple stroke validation
    |      1. Clear canvas
    |      2. python scripts/draw_stroke.py --points "100,200 300,200 500,400"
    |      3. python scripts/capture_drawing.py screen.png
    |      4. python scripts/compare_canvas.py screen.png baseline.png
    |
    |-- Signature capture testing
    |      1. Navigate to signature screen
    |      2. python scripts/draw_stroke.py --signature cursive
    |      3. Verify signature accepted
    |
    |-- Complex drawing automation
    |      1. Use draw_stroke.py for each stroke
    |      2. Add delays between strokes
    |      3. Capture and compare final result
    |
    |-- Performance testing
    |      1. Draw many strokes rapidly
    |      2. Monitor frame drops via logcat
    |      3. Verify no stroke data loss
```

## Drawing Stroke Simulation

### Using draw_stroke.py

```bash
# Draw a simple line
python scripts/draw_stroke.py --points "100,200 500,200"

# Draw a curved path (more points = smoother curve)
python scripts/draw_stroke.py --points "100,500 200,300 300,200 400,300 500,500"

# Draw with specific duration (ms)
python scripts/draw_stroke.py --points "100,200 500,400" --duration 500

# Draw multiple strokes (comma-separated)
python scripts/draw_stroke.py --strokes "100,200 300,200;400,100 400,400"

# Simulate signature
python scripts/draw_stroke.py --signature "cursive"
```

### Direct ADB Touch Simulation

```bash
# Single stroke (swipe)
adb shell input swipe 100 200 500 400 300

# Multi-point stroke (touchscreen)
adb shell input touchscreen swipe 100 200 300 200 500
adb shell input touchscreen swipe 300 200 300 400 500

# Tap (dot)
adb shell input tap 250 300
```

### Python Touch Automation

```python
import subprocess
import time

def draw_stroke(points: list[tuple[int, int]], duration_ms: int = 300):
    """Draw a stroke through multiple points"""
    if len(points) < 2:
        return

    # ADB swipe only supports 2 points, chain for multi-point
    for i in range(len(points) - 1):
        x1, y1 = points[i]
        x2, y2 = points[i + 1]
        segment_duration = duration_ms // (len(points) - 1)
        subprocess.run([
            'adb', 'shell', 'input', 'swipe',
            str(x1), str(y1), str(x2), str(y2), str(segment_duration)
        ])
        time.sleep(0.05)  # Small gap between segments

def draw_circle(center_x: int, center_y: int, radius: int, points: int = 16):
    """Draw a circle using touch points"""
    import math
    circle_points = []
    for i in range(points + 1):
        angle = 2 * math.pi * i / points
        x = int(center_x + radius * math.cos(angle))
        y = int(center_y + radius * math.sin(angle))
        circle_points.append((x, y))
    draw_stroke(circle_points, duration_ms=1000)

# Usage
draw_stroke([(100, 200), (300, 200), (500, 400)])
draw_circle(400, 600, 100)
```

## Canvas Image Comparison

### Using compare_canvas.py

```bash
# Compare current screen to baseline
python scripts/compare_canvas.py current.png baseline.png

# With tolerance (0-100, higher = more lenient)
python scripts/compare_canvas.py current.png baseline.png --tolerance 5

# Crop to canvas area before comparing
python scripts/compare_canvas.py current.png baseline.png --crop "100,200,800,1000"

# Generate diff image
python scripts/compare_canvas.py current.png baseline.png --diff diff.png
```

### Python Image Comparison

```python
from PIL import Image
import numpy as np

def compare_images(img1_path: str, img2_path: str, tolerance: float = 0.02) -> bool:
    """Compare two images, return True if similar within tolerance"""
    img1 = np.array(Image.open(img1_path))
    img2 = np.array(Image.open(img2_path))

    if img1.shape != img2.shape:
        return False

    diff = np.abs(img1.astype(float) - img2.astype(float))
    diff_ratio = np.sum(diff > 10) / diff.size

    return diff_ratio <= tolerance

def get_canvas_region(screenshot_path: str, bounds: tuple) -> Image:
    """Crop screenshot to canvas region"""
    x1, y1, x2, y2 = bounds
    img = Image.open(screenshot_path)
    return img.crop((x1, y1, x2, y2))

# Usage
if compare_images('current.png', 'baseline.png'):
    print("Drawing matches baseline")
else:
    print("Drawing differs from baseline")
```

## Screenshot Capture

### Using capture_drawing.py

```bash
# Capture full screen
python scripts/capture_drawing.py screen.png

# Capture specific region (canvas area)
python scripts/capture_drawing.py canvas.png --bounds "50,200,750,1200"

# Wait for drawing to stabilize before capture
python scripts/capture_drawing.py screen.png --wait 500
```

### Direct ADB Screenshot

```bash
# Capture and pull
adb exec-out screencap -p > screen.png

# Capture to device, then pull
adb shell screencap /sdcard/screen.png
adb pull /sdcard/screen.png
```

## Common Test Patterns

### Pattern 1: Basic Stroke Verification

```python
# 1. Clear canvas
adb_tap(clear_button_x, clear_button_y)
time.sleep(0.3)

# 2. Draw stroke
draw_stroke([(100, 300), (500, 300)])
time.sleep(0.3)

# 3. Capture result
subprocess.run(['adb', 'exec-out', 'screencap', '-p'],
               stdout=open('result.png', 'wb'))

# 4. Compare
assert compare_images('result.png', 'baseline_horizontal_line.png')
```

### Pattern 2: Undo/Redo Testing

```python
# Draw stroke
draw_stroke([(100, 300), (500, 300)])
capture_screenshot('after_stroke.png')

# Undo
adb_tap(undo_button_x, undo_button_y)
capture_screenshot('after_undo.png')

# Verify canvas is clear
assert compare_images('after_undo.png', 'empty_canvas.png')

# Redo
adb_tap(redo_button_x, redo_button_y)
capture_screenshot('after_redo.png')

# Verify stroke restored
assert compare_images('after_redo.png', 'after_stroke.png')
```

### Pattern 3: Signature Capture Flow

```python
# Navigate to signature screen
adb_tap(sign_button_x, sign_button_y)
wait_for_element('signature_canvas')

# Draw signature-like strokes
signature_strokes = [
    [(100, 400), (150, 350), (200, 400), (250, 450)],  # First letter
    [(280, 350), (320, 450)],  # Connecting stroke
    [(350, 400), (400, 350), (450, 400)],  # Second letter
]

for stroke in signature_strokes:
    draw_stroke(stroke, duration_ms=200)
    time.sleep(0.1)

# Submit signature
adb_tap(submit_button_x, submit_button_y)

# Verify accepted
assert wait_for_text('Signature saved')
```

## Finding Canvas Bounds

Use UI Automator to find the drawing canvas area:

```bash
# Dump UI hierarchy
adb shell uiautomator dump /sdcard/ui.xml
adb pull /sdcard/ui.xml

# Find canvas element
grep -E 'Canvas|DrawingView|canvas|drawing' ui.xml
```

Look for `bounds="[x1,y1][x2,y2]"` in the output.

## Best Practices

- **Clear canvas before each test** - Start from known state
- **Use relative coordinates** - Calculate from canvas bounds, not screen
- **Add waits between strokes** - Allow rendering to complete
- **Store baseline images** - Version control golden images
- **Test on multiple resolutions** - Canvas bounds differ per device
- **Use tolerance in comparisons** - Anti-aliasing causes minor differences

## Reference Files

- **references/gesture_patterns.md** - Common drawing gestures and signatures
- **references/image_comparison.md** - Advanced image comparison techniques
- **references/troubleshooting.md** - Common issues and solutions

## Example Scripts

- **examples/basic_stroke_test.py** - Simple line drawing test
- **examples/signature_flow.py** - Full signature capture workflow
- **examples/undo_redo_test.py** - Undo/redo functionality testing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hungson175) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
