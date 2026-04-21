---
name: badger-2350-dev
description: Development environment setup and workflow for Universe 2025 (Tufty) Badge with MonaOS. Use when setting up the badge, flashing firmware, debugging, or working with the development toolchain. Use when this capability is needed.
metadata:
  author: johnlindquist
---

# Universe 2025 Badge Development

Help develop, flash, and debug applications for the Universe 2025 (Tufty) Badge and its **MonaOS** launcher system.

## Important: MonaOS & API

The Universe 2025 Badge uses:
- **MonaOS**: Built-in app launcher that auto-discovers apps in `/system/apps/` directory
- **badgeware Module**: Custom API with screen, brushes, shapes, io, Image, PixelFont
- **Display**: 160x120 framebuffer (pixel-doubled to 320x240)
- **App Structure**: Apps are **directories** containing `__init__.py`, `icon.png`, and optional `assets/`
- **Entry Point**: Apps must implement `update()` function called every frame

When developing MonaOS apps:
1. Use the `badgeware` module API
2. Create app as directory with `__init__.py` and `icon.png`
3. Install to `/system/apps/my_app/` directory
4. HOME button exits to menu automatically
5. Default menu shows 6 apps - enable pagination for more

## Board Specifications

- **Processor**: RP2350 dual-core ARM Cortex-M33 @ 200MHz
- **Memory**: 512KB SRAM, 16MB QSPI XiP flash
- **Display**: 320x240 full-color IPS (160x120 framebuffer pixel-doubled)
- **Connectivity**: 2.4GHz WiFi, Bluetooth 5
- **Power**: USB-C charging, 1000mAh rechargeable battery (up to 8 hours runtime)
- **Special Features**: IR receiver/transmitter, 4-zone LED backlight
- **Buttons**: 5 front-facing (A, B, C, UP, DOWN) + HOME button
- **Expansion**: 4 GPIO pins, Qw/ST and SWD ports
- **Primary Language**: MicroPython with badgeware module + MonaOS

## Development Setup

### 1. Install toolchain

For MicroPython development:
```bash
# Install Thonny IDE (recommended for beginners)
brew install --cask thonny

# Or install command-line tools
pip install esptool adafruit-ampy mpremote
```

For C++ development:
```bash
# Install Pico SDK
git clone https://github.com/raspberrypi/pico-sdk.git
export PICO_SDK_PATH=/path/to/pico-sdk
```

### 2. Connect to the badge

```bash
# List connected devices
ls /dev/tty.usb*

# Connect via serial (MicroPython REPL)
screen /dev/tty.usbmodem* 115200
# Exit screen: Ctrl+A then K
```

### 3. Flash firmware

```bash
# Put badge in bootloader mode (hold BOOT button, press RESET)

# Flash MicroPython firmware
esptool.py --port /dev/tty.usbmodem* write_flash 0x0 firmware.uf2
```

## Common Development Tasks

### Test app temporarily (doesn't save)
```bash
mpremote connect /dev/tty.usbmodem* run my_app/__init__.py
```

### Install MonaOS app using USB Mass Storage Mode

**⚠️ CRITICAL**: `/system/apps/` is READ-ONLY via mpremote. You MUST use USB Mass Storage Mode to install apps.

```bash
# Step 1: Enter Mass Storage Mode
# - Press RESET button TWICE quickly (double-click)
# - Badge appears as "BADGER" drive

# Step 2: Copy app to badge
# macOS/Linux:
cp -r my_app /Volumes/BADGER/apps/

# Windows:
xcopy my_app D:\apps\my_app\ /E /I

# Step 3: Exit Mass Storage Mode
# - Eject BADGER drive safely
diskutil eject /Volumes/BADGER  # macOS
# - Press RESET once to reboot

# Your app now appears in MonaOS launcher!
```

**File System Mapping**:
- `/Volumes/BADGER/apps/` → `/system/apps/` on badge
- `/Volumes/BADGER/assets/` → `/system/assets/` on badge

### List MonaOS apps (read-only view)
```bash
mpremote connect /dev/tty.usbmodem* ls /system/apps
```

**⚠️ Note**: Install the paginated menu for unlimited apps (default shows 6):
- Download: https://raw.githubusercontent.com/badger/home/refs/heads/main/badge/apps/menu/__init__.py
- Replace `/Volumes/BADGER/apps/menu/__init__.py` in Mass Storage Mode

### List files in writable storage
```bash
mpremote connect /dev/tty.usbmodem* ls /storage
```

### Download file from badge
```bash
mpremote connect /dev/tty.usbmodem* cp :/system/apps/my_app/__init__.py local_backup.py
```

## Project Structure

MonaOS app structure on your computer:
```
my_app/               # MonaOS app directory
├── __init__.py       # Entry point with update() function (required)
├── icon.png          # 24x24 PNG icon for launcher (required)
├── assets/           # Optional: app resources (auto-added to path)
│   ├── sprites.png
│   ├── font.ppf
│   └── data.json
└── README.md         # Optional: app documentation
```

Multiple apps in development:
```
badge-project/
├── my_app/           # First MonaOS app
│   ├── __init__.py
│   └── icon.png
├── game_app/         # Second MonaOS app
│   ├── __init__.py
│   ├── icon.png
│   └── assets/
│       └── sprites.png
├── requirements.txt  # Python tools
└── deploy.sh         # Deployment script
```

## Debugging

### Check badge logs
```python
# In REPL
import sys
sys.print_exception(e)  # Print full exception traceback
```

### Test display
```python
from badgeware import screen, display, brushes

# Clear with black
screen.brush = brushes.color(0, 0, 0)
screen.clear()

# White text
screen.brush = brushes.color(255, 255, 255)
screen.text("Hello Badge!", 10, 10, 2)
display.update()
```

### Test WiFi
```python
import network
wlan = network.WLAN(network.STA_IF)
wlan.active(True)
wlan.connect('SSID', 'password')
print(wlan.isconnected())
print(wlan.ifconfig())  # IP address info
```

## Power Management

```python
import machine
import badgeware

# Check battery level
battery = badgeware.get_battery_level()
print(f"Battery: {battery}%")

# Check if USB connected
usb = badgeware.get_usb_connected()
print(f"USB: {usb}")

# Light sleep (for delays)
machine.lightsleep(1000)  # Sleep 1 second

# Deep sleep (wake on button press - saves significant power)
machine.deepsleep()
```

## Tips for MonaOS Apps

- MonaOS apps use `update()` function called every frame
- Optimize `update()` - it runs continuously
- Use `io.ticks` for animations instead of time.time()
- Minimize allocations in `update()` to reduce GC pauses
- Use `try/except` blocks to prevent crashes
- Test with USB power first, then battery
- Apps automatically return to menu when HOME button pressed

## Common Issues

**Badge not detected**: Check USB cable, try different port, press RESET button

**Out of memory**: Reduce allocations in `update()`, use generators, call `gc.collect()`, free variables with `del`

**Display not updating**: MonaOS automatically updates after `update()` returns - no manual update needed

**App not in menu**: Check uploaded to `/system/apps/my_app/`, verify icon.png exists, may need pagination: https://badger.github.io/hack/menu-pagination/

**WiFi connection fails**: Check credentials, verify 2.4GHz band, restart badge

## Resources

### Official Badge Resources
- **Getting Started**: https://badger.github.io/get-started/ - Overview and setup
- **About Badge**: https://badger.github.io/about-badge/ - Hardware specifications
- **Hacks**: https://badger.github.io/hacks/ - Beginner to advanced customization tutorials
- **Apps**: https://badger.github.io/apps/ - Loadable MonaOS apps (Commits, Snake)
- **Source Code**: https://github.com/badger/home/tree/main/badgerware - Official MonaOS app code and API docs

### API Documentation
- **badgeware modules**: https://github.com/badger/home/tree/main/badgerware - shapes, brushes, io, Image, PixelFont, Matrix

### Development Resources
- **MicroPython docs**: https://docs.micropython.org/
- **WiFi/Network**: https://docs.micropython.org/en/latest/rp2/quickref.html#wlan
- **Community**: Badger GitHub discussions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnlindquist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
