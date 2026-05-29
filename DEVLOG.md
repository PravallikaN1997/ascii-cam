# DEVLOG — ASCII CAM

A running log of every build decision, fix, and iteration.

---

## 2026-05-29

### v7.x — Canvas Pipeline Fix (final)

**Problem:** display canvas showing black even though face detection was working.

**Root cause (from debugging):**
1. `canvas.style.width: 100%` was stretching the bitmap — internal resolution (set by JS) and CSS display size were different, causing invisible/distorted render
2. `globalAlpha` was being left at `0.5` after glitch mode drawing, making all subsequent frames render at half opacity → black
3. `transform: translate(-50%, -50%)` centering was fighting dynamic canvas resizing

**Fix:**
- Set `dispCanvas.style.width = canvasW + 'px'` and `dispCanvas.style.height = canvasH + 'px'` every frame to match internal resolution
- Reset `globalAlpha = 1` and `globalCompositeOperation = 'source-over'` at start of every draw cycle
- Canvas CSS: no `width/height` override, just `transform` centering

**Key learning:** Canvas has two separate sizes — internal pixel buffer (`canvas.width/height`) and CSS display size. If they differ, browser stretches the bitmap and everything breaks. Always keep them in sync.

---

### v7 — Single Canvas Architecture

**Problem:** download never matched the screen. Two separate renderers (DOM text for display, canvas redraw for export) always drifted apart.

**Decision:** rebuild display layer as a canvas element. One renderer draws to `#display-canvas`. Download is `drawImage(dispCanvas)`. Zero drift possible.

**Reference:** okaypranjul/Ascii-Yourself uses same pattern in React — canvas renders, canvas saves.

---

### v6 — Removed GIF, Fixed Capture

**Problem:** GIF download was outputting PNG, wrong colors, single frame.

**Root cause:** `gif.js` worker couldn't load from CDN when running as local file (browser blocks cross-origin workers for `file://` protocol). Inlined worker as Blob URL but encoding was still unstable.

**Decision:** removed GIF entirely. Replaced with 3-second countdown PNG capture. Simpler, more reliable, better quality.

---

### v5 — INSPO Charset Discovery

**Key insight:** the reference ASCII art (okaypranjul's TikTok) uses extremely low character variety — mostly `i`, `1`, `;`, `:`, `,`, `.`. NOT `@#$%`. Low variety = smooth density gradients = face reads as shape not noise.

Added INSPO charset: `'i1;:,. '`

This single change made the output look 10x closer to reference.

---

### v4 — BG Cut

**Problem:** background (walls, monitors, desk) rendering as ASCII, polluting the image.

**Solution:** BG CUT threshold — pixels below value → forced to space character. Separates face from environment cleanly. Works with slider for per-environment tuning.

---

### v3 — Auto Calibrate

**Problem:** gain/contrast defaults wrong for different lighting environments — users had to manually tune every time.

**Solution:** sample face zone (center 50% of frame) for 30 frames on startup, compute average brightness, derive gain/contrast mathematically. Target processed face brightness ≈ 0.55.

---

### v2 — Feature Expansion

Added: COLOR mode (per-character RGB from camera), GLITCH mode (chromatic aberration layers), audio reactivity (Web Audio API), gesture detection (zone-based brightness heuristic), rain mode.

---

### v1 — Initial Proof of Concept

Core loop: `getUserMedia` → mirror video to canvas → sample pixel grid → map luminance to charset → render as `<pre>` text → motion detection triggers HUD.

Confirmed: real-time ASCII from webcam works at 60fps in browser with zero dependencies.

---

## Architecture Notes

### Render Pipeline (v7+)
```
camera frame
    ↓
mirror + draw to hidden <canvas>
    ↓  
sample pixel grid (cols × rows)
    ↓
luminance → BG cut → contrast → gain → charset index
    ↓
draw characters to display <canvas> (dispCtx.fillText)
    ↓
capture = dispCanvas.toDataURL()
```

### Why canvas over `<pre>` text
- CSS font rendering ≠ canvas font rendering — they use different metrics
- `<pre>` text is affected by browser zoom, subpixel rendering, line-height quirks
- Canvas `fillText` is deterministic — same input always = same output
- Canvas can be directly copied: `drawImage(dispCanvas)` = pixel-perfect export

### Character sets
- INSPO: `i1;:,. ` — uniform texture, smooth density gradients
- CLASSIC: `@#S%?*+;:,. ` — traditional, high variety, noisier
- BLOCKS: `█▓▒░· ` — geometric, bold
- MINIMAL: `@#:. ` — sparse, editorial
- MATRIXCS: `ｱｲｳｴｵ01ｿ:. ` — Japanese katakana mix

### Motion detection
Compare current pixel luminance array to previous frame. Mean absolute difference > threshold → awaken HUD. Higher difference → trigger glitch burst.

### Gesture detection
Divide frame into zones. Face zone = center 40% width, 90% height. Bright pixels outside face zone = gesture. Zone (left/right/both/top) determines which response message fires.
