---
name: camsnap
description: Capture frames or clips from RTSP/ONVIF cameras. Grabs snapshots, video clips, and motion events from IP cameras, security cameras, and video streams. Use when the user wants to take a snapshot from a camera, record a clip from an RTSP stream, monitor motion on a security camera, discover ONVIF devices on the network, or configure camera access for automated surveillance capture. Use when this capability is needed.
metadata:
  author: elizaos
---

# camsnap

Use `camsnap` to grab snapshots, clips, or motion events from configured cameras.

Setup

- Config file: `~/.config/camsnap/config.yaml`
- Add camera: `camsnap add --name kitchen --host 192.168.0.10 --user user --pass pass`

Common commands

- Discover: `camsnap discover --info`
- Snapshot: `camsnap snap kitchen --out shot.jpg`
- Clip: `camsnap clip kitchen --dur 5s --out clip.mp4`
- Motion watch: `camsnap watch kitchen --threshold 0.2 --action '...'`
- Doctor: `camsnap doctor --probe`

Notes

- Requires `ffmpeg` on PATH.
- Prefer a short test capture before longer clips.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elizaos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
