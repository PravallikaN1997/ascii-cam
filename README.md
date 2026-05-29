# ASCII CAM

> A surveillance terminal that turns you into data. Live ASCII webcam art with motion tracking, gesture detection, and instant download.

![ASCII CAM](https://img.shields.io/badge/status-live-00ff41?style=flat-square&labelColor=000) ![License](https://img.shields.io/badge/license-MIT-00ff41?style=flat-square&labelColor=000) ![No Dependencies](https://img.shields.io/badge/dependencies-zero-00ff41?style=flat-square&labelColor=000)

---

## What it does

Your webcam feed converts to live ASCII art in real time. The system watches you — tracking motion, detecting your face, reading your gestures. Move and the HUD awakens. Stay still and it waits.

---

## Features

### Modes
| Mode | Description |
|------|-------------|
| `MATRIX` | Green characters on black — the classic |
| `RETRO` | White/grey, dense, editorial |
| `DENSE` | Amber tones, high contrast |
| `COLOR` | Each character maps to its real camera color |
| `SPARSE` | Minimal scattered characters, editorial feel |
| `GLITCH` | Chromatic aberration + VHS scanlines + row displacement |

### Controls
- **Font** — character size (affects density)
- **Gain** — brightness amplification
- **Contrast** — dynamic range
- **BG Cut** — threshold to remove background (push right to isolate face)
- **Spacing** — letter spacing between characters
- **Charset** — INSPO / CLASSIC / BLOCKS / MINIMAL / MATRIX

### Smart Systems
- **Motion detection** — HUD brackets, signal data, and log lines awaken when you move
- **Face detection** — `[ FACE DETECTED ]` pulses when your face is in frame
- **Gesture detection** — raises different alerts based on hand position (left, right, both, overhead)
- **Audio reactivity** — mic input drives character density in real time
- **⟳ OPTIMIZE** — reads your ambient light and auto-sets gain/contrast/bg cut
- **3-second countdown capture** — pose, wait, download

### Download
Single canvas pipeline — what you see is exactly what gets saved. No re-rendering, no drift.

---

## Usage

**Local**
Just open `index.html` in Chrome. Allow camera + mic when prompted.

**Hosted**
Live at → [ascii-cam.vercel.app](https://ascii-cam.vercel.app)

No install. No dependencies. No API keys. Pure browser.

---

## Tips

- **BG bleeding on sides?** → drag BG CUT right to 30–40%
- **Face too dim?** → increase GAIN
- **Want the inspo look?** → MATRIX mode + INSPO charset + Font 10-12px
- **Full body ASCII?** → lower BG CUT to 5-10%, step back from camera
- **Gesture reactions** → raise one or both hands outside your face zone

---

## Devlog

### v7 — Canvas Pipeline (current)
- Rebuilt render pipeline from `<pre>` text to `<canvas>` — single source of truth
- Download now copies display canvas directly — zero drift between preview and export
- Fixed `globalAlpha` reset bug causing black screen in glitch mode
- Proper CSS/internal canvas size sync (`canvas.style.width` = `canvas.width`)

### v6 — Countdown Capture + Controls
- Removed GIF (unstable), replaced with 3-second countdown PNG capture
- Controls bar redesigned — bigger, readable, dividers between sections
- GIF button removed after worker blob issues with local file protocol

### v5 — INSPO Charset + Adaptive Calibration
- Added INSPO charset (`i1;:,. `) — matches the dense uniform look from reference
- Auto-calibrate now samples face region specifically, not full frame
- SPACING slider added — independent letter-spacing control
- Preset locked to working settings (font 15px, gain 1.3, contrast 0.5, BG cut 29%)

### v4 — Background Cut + Face Detection
- BG CUT slider — forces pixels below threshold to pure black
- `[ FACE DETECTED ]` badge — heuristic center-zone brightness check
- OPTIMIZE button — face-aware brightness sampling

### v3 — Auto Calibrate + Full Viewport
- Grid fills entire viewport edge to edge
- Auto-calibrate on startup — reads ambient light, sets optimal gain/contrast
- ⟳ OPTIMIZE button added

### v2 — Feature Expansion
- 6 modes: RETRO, MATRIX, DENSE, RAIN, COLOR, SPARSE
- 4 charsets including Japanese katakana
- Glitch burst on heavy motion
- Audio reactivity via Web Audio API
- Rain mode — vertical matrix streams through face silhouette

### v1 — Initial Build
- Live webcam → ASCII conversion
- Motion detection → HUD awakens
- Dormant cursor when still
- 3 modes, capture + download

---

## Tech

Pure HTML + vanilla JavaScript. No frameworks, no build step, no dependencies.

- `getUserMedia` — webcam + mic access
- `Canvas 2D API` — pixel sampling and ASCII rendering
- `Web Audio API` — microphone frequency analysis
- `FaceDetector API` (Chrome) — face bounding box for OPTIMIZE
- `requestAnimationFrame` — 60fps render loop

---

## License

MIT — use it, fork it, build on it.

---

*Built with Claude Sonnet*
