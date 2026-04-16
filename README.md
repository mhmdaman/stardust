# ✦ Stardust Gesture Engine

A real-time, gesture-controlled particle system built with Three.js and MediaPipe Hands. Control a living cloud of 12,000 particles using nothing but your hands and a webcam.

**Live demo:** [mhmdaman.github.io/stardust](https://mhmdaman.github.io/stardust/index.html)

---

## Gestures

| Gesture | Effect |
|---|---|
| **Pinch** (thumb + index) | Agitates particles with noise |
| **Raise hand** | Morphs sphere → heart shape |
| **Fist** | Morphs → Saturn with tilted rings |
| **Spread both arms** | Flies camera through the stardust field |

---

## Features

- **12,000-particle GPU-rendered system** via custom GLSL vertex/fragment shaders
- **Three morph targets** — sphere, heart, and Saturn — blended entirely on the GPU
- **Saturn ring system** with multi-band density distribution and animated ring-tilt reveal
- **Fist hysteresis** — debounced over multiple frames to prevent flickering transitions
- **Spring-damper smoothing** on all morph transitions for cinematic motion
- **Dive mode** — spreading both arms flies the camera through the field with FOV expansion and per-particle scatter
- **Additive blending** for a luminous, glowing look
- Hand tracking via **MediaPipe Hands** (runs fully in-browser, no backend)

---

## Tech Stack

| Library | Role |
|---|---|
| [Three.js r128](https://threejs.org/) | WebGL renderer, geometry, shader materials |
| [MediaPipe Hands](https://google.github.io/mediapipe/solutions/hands) | Real-time hand landmark detection |
| MediaPipe Camera Utils | Webcam feed management |

No build tools, no bundler — pure browser JS.

---

## How It Works

### Particle Morphing
Each particle has three positions stored as GPU buffer attributes (`position`, `targetPos`, `saturnPos`). The vertex shader uses `mix()` to interpolate between them based on `uMorph` and `uSaturn` uniforms, updated every frame from hand landmark data.

### Gesture Detection
- **Pinch**: Euclidean distance between thumb tip (landmark 4) and index tip (landmark 8)
- **Hand height**: Wrist Y-coordinate drives the sphere→heart morph
- **Fist**: Checks if fingertip Y > PIP joint Y for 3+ fingers; gated with hysteresis counters to avoid flicker
- **Spread arms**: Wrist-to-wrist distance across both detected hands

### Saturn Rings
Ring particles are distributed across four radial bands with varying density, simulating the Cassini Division and inner/outer ring structure. A `uRingTilt` uniform rotates the ring plane around the X-axis so rings face the camera when Saturn mode activates.

### Smoothing
All transitions use a critically-damped spring-damper (`smoothDamp`) rather than simple lerp, giving morph transitions a natural ease-in/ease-out feel.

---

## Running Locally

No install needed. Just serve the file over HTTP (required for webcam access):

```bash
# Python
python -m http.server 8080

# Node
npx serve .
```

Then open `http://localhost:8080` and allow webcam access.

> Webcam access requires HTTPS or localhost — opening `index.html` directly as a `file://` URL won't work.

---

## Browser Support

Works in any modern browser with WebGL and webcam support. Chrome/Edge recommended for best MediaPipe performance.

---

