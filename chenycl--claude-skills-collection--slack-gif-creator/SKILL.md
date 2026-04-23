---
name: slack-gif-creator
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Slack GIF Creator

Create animated GIFs optimized for Slack with proper constraints and validation.

## Slack Constraints

### Custom Emoji Requirements
| Property | Limit |
|----------|-------|
| File size | Max 128 KB |
| Dimensions | Max 128x128 px |
| Format | GIF, PNG, JPG |
| Aspect ratio | Square preferred |

### Message GIFs
| Property | Recommended |
|----------|-------------|
| Width | 200-400 px |
| Height | 200-400 px |
| File size | Under 2 MB |
| Duration | 2-5 seconds |
| Frame rate | 10-15 fps |

## Creating GIFs with Python

### Using Pillow
```python
from PIL import Image, ImageDraw, ImageFont
import io

def create_animated_gif(frames, output_path, duration=100):
    """
    Create a GIF from a list of PIL Image frames.

    Args:
        frames: List of PIL Image objects
        output_path: Output file path
        duration: Duration per frame in milliseconds
    """
    frames[0].save(
        output_path,
        save_all=True,
        append_images=frames[1:],
        duration=duration,
        loop=0,  # 0 = infinite loop
        optimize=True
    )
```

### Simple Animation Example
```python
from PIL import Image, ImageDraw

def create_bouncing_ball_gif(output_path):
    frames = []
    size = 128
    ball_radius = 20

    # Create 20 frames
    for i in range(20):
        img = Image.new('RGBA', (size, size), (255, 255, 255, 0))
        draw = ImageDraw.Draw(img)

        # Calculate ball position (bouncing)
        y = int(abs(math.sin(i * 0.3) * 50) + 30)

        # Draw ball
        draw.ellipse(
            [size//2 - ball_radius, y - ball_radius,
             size//2 + ball_radius, y + ball_radius],
            fill='#FF6B6B'
        )

        frames.append(img)

    # Save as GIF
    frames[0].save(
        output_path,
        save_all=True,
        append_images=frames[1:],
        duration=50,
        loop=0,
        transparency=0,
        disposal=2
    )
```

### Text Animation
```python
from PIL import Image, ImageDraw, ImageFont

def create_typing_gif(text, output_path):
    frames = []
    size = (200, 50)

    try:
        font = ImageFont.truetype("/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 24)
    except:
        font = ImageFont.load_default()

    # Create frame for each character
    for i in range(len(text) + 1):
        img = Image.new('RGB', size, 'white')
        draw = ImageDraw.Draw(img)
        draw.text((10, 10), text[:i] + "_", fill='black', font=font)
        frames.append(img)

    # Add cursor blink at end
    for _ in range(3):
        img = Image.new('RGB', size, 'white')
        draw = ImageDraw.Draw(img)
        draw.text((10, 10), text, fill='black', font=font)
        frames.append(img)

        img = Image.new('RGB', size, 'white')
        draw = ImageDraw.Draw(img)
        draw.text((10, 10), text + "_", fill='black', font=font)
        frames.append(img)

    frames[0].save(
        output_path,
        save_all=True,
        append_images=frames[1:],
        duration=100,
        loop=0
    )
```

## Optimization Techniques

### Reduce File Size
```python
from PIL import Image

def optimize_gif(input_path, output_path, max_colors=64):
    """Optimize GIF for smaller file size."""
    img = Image.open(input_path)

    # Reduce colors
    frames = []
    try:
        while True:
            frame = img.copy()
            frame = frame.convert('P', palette=Image.ADAPTIVE, colors=max_colors)
            frames.append(frame)
            img.seek(img.tell() + 1)
    except EOFError:
        pass

    frames[0].save(
        output_path,
        save_all=True,
        append_images=frames[1:],
        duration=img.info.get('duration', 100),
        loop=0,
        optimize=True
    )
```

### Reduce Dimensions
```python
def resize_gif(input_path, output_path, max_size=128):
    """Resize GIF to fit within max dimensions."""
    img = Image.open(input_path)

    # Calculate new size maintaining aspect ratio
    ratio = min(max_size / img.width, max_size / img.height)
    new_size = (int(img.width * ratio), int(img.height * ratio))

    frames = []
    try:
        while True:
            frame = img.copy()
            frame = frame.resize(new_size, Image.LANCZOS)
            frames.append(frame)
            img.seek(img.tell() + 1)
    except EOFError:
        pass

    frames[0].save(
        output_path,
        save_all=True,
        append_images=frames[1:],
        duration=img.info.get('duration', 100),
        loop=0,
        optimize=True
    )
```

### Reduce Frame Count
```python
def reduce_frames(input_path, output_path, keep_every=2):
    """Keep only every Nth frame."""
    img = Image.open(input_path)

    frames = []
    frame_num = 0
    try:
        while True:
            if frame_num % keep_every == 0:
                frames.append(img.copy())
            img.seek(img.tell() + 1)
            frame_num += 1
    except EOFError:
        pass

    if frames:
        frames[0].save(
            output_path,
            save_all=True,
            append_images=frames[1:],
            duration=img.info.get('duration', 100) * keep_every,
            loop=0
        )
```

## Animation Ideas

### Emoji Reactions
- Thumbs up appearing
- Heart beating/pulsing
- Star spinning
- Checkmark drawing itself
- Fire flickering

### Status Indicators
- Loading spinner
- Progress dots
- Typing indicator (...)
- Pulse animation

### Celebrations
- Confetti falling
- Sparkles twinkling
- Party popper exploding
- Balloons floating

## Validation Script
```python
from PIL import Image
import os

def validate_slack_gif(path):
    """Check if GIF meets Slack requirements."""
    issues = []

    # Check file size
    file_size = os.path.getsize(path)
    if file_size > 128 * 1024:
        issues.append(f"File size {file_size/1024:.1f}KB exceeds 128KB limit")

    # Check dimensions
    img = Image.open(path)
    if img.width > 128 or img.height > 128:
        issues.append(f"Dimensions {img.width}x{img.height} exceed 128x128")

    # Check format
    if img.format != 'GIF':
        issues.append(f"Format is {img.format}, not GIF")

    if issues:
        print("❌ Issues found:")
        for issue in issues:
            print(f"  - {issue}")
        return False
    else:
        print("✅ GIF meets Slack emoji requirements!")
        return True
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
