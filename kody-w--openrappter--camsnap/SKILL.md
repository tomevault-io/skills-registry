---
name: camsnap
description: Capture frames or clips from RTSP/ONVIF cameras. Use when this capability is needed.
metadata:
  author: kody-w
---

# CamSnap

Capture images and video clips from IP cameras (RTSP/ONVIF).

## Capture a Frame

```bash
camsnap capture --url rtsp://camera-ip:554/stream --output /tmp/frame.jpg
```

## List Cameras

```bash
camsnap discover
```

## Record a Clip

```bash
camsnap record --url rtsp://camera-ip:554/stream --duration 10 --output /tmp/clip.mp4
```

## Continuous Monitoring

```bash
camsnap watch --url rtsp://camera-ip:554/stream --interval 60 --output /tmp/frames/
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
