# Neon Hand — Gesture Engine (v3)

A single-page, browser-based hand-tracking visualizer. It uses
[MediaPipe Hands](https://github.com/google/mediapipe) to track up to
two hands from a webcam feed in real time and renders a neon HUD —
glowing skeletons, gesture-driven light rays, particle trails, and a
projectile/orb system — entirely on `<canvas>`. No build step, no
backend, no dependencies to install.

## Features

| Feature | Description |
|---|---|
| Hand tracking | Up to 2 hands via MediaPipe Hands, loaded from CDN |
| Gesture recognition | `FIST`, `OPEN PALM`, `PINCH`, `POINT`, `PEACE` detected from landmark geometry |
| Reactive themes | Gestures on Hand A switch the color theme: `FIST` → fire, `PEACE` → ice, `OPEN PALM` → cyber |
| Pinch-to-fire | Holding a pinch for ~18 frames charges and launches a glowing projectile that bounces off screen edges |
| Two-hand interaction | Dashed energy line between wrists, live pixel distance readout, and a burst effect when both hands pinch close together |
| Particle system | Trailing particles on fingertips, projectiles, and theme transitions |
| Procedural audio | All sound effects (pinch, fist, fire, theme switch, burst) are synthesized via the Web Audio API — no audio files |
| Live HUD | FPS counter, per-hand gesture label, per-hand extended-finger count, hand distance |

## Screenshots

| Cyber (default) | Fire | Ice |
|---|---|---|
| ![](screenshots/screenshot_openpalm_cyber.png) | ![](screenshots/screenshot_fist_fire.png) | ![](screenshots/screenshot_peace_ice.png) |

| Pinch-charge | Two-hand bridge |
|---|---|
| ![](screenshots/screenshot_pinch_charge.png) | ![](screenshots/screenshot_two_hand_bridge.png) |
## Getting started

MediaPipe's WASM/camera assets require the page to be served over
HTTP — opening `index.html` directly via `file://` will not work.

```bash
npx serve .
# or
python3 -m http.server 8080
```

Open the printed URL and grant camera access when prompted.

### Requirements

- A webcam
- A browser with WebRTC + WebAssembly support (current Chrome, Edge,
  Firefox, or Safari)
- An internet connection (MediaPipe libraries load from jsDelivr's CDN)

## Controls

| Gesture | Effect |
|---|---|
| ✊ Fist | Switch to fire theme |
| ✌️ Peace | Switch to ice theme |
| 🖐️ Open palm | Switch to cyber theme (default) |
| 🤏 Pinch + hold | Charge and fire a projectile |
| Both hands pinching, close together | Energy burst |

The **SOUND** button (top center) toggles all audio on/off.

## Project structure

```
.
├── index.html                          # markup, styles, and all application logic in one file
├── screenshot_openpalm_cyber.png       # open palm gesture, cyber theme
├── screenshot_fist_fire.png            # fist gesture, fire theme
├── screenshot_peace_ice.png            # peace sign, ice theme
├── screenshot_pinch_charge.png         # pinch gesture charging a projectile
└── screenshot_two_hand_bridge.png      # two-hand fingertip energy bridge
```

## How it works, briefly

- `Hands` (MediaPipe) streams webcam frames and returns 21 landmarks
  per detected hand; `onResults()` is the per-frame render loop.
- `detectGesture()` classifies a hand's pose from landmark distances
  and thresholds — no ML classifier beyond MediaPipe's own model.
- All visual effects (`drawGlowLine`, `drawGlowDot`, `drawExtendedRay`,
  particles, projectiles) are drawn on two stacked canvases: `canvas`
  for the skeleton/rays, `fx` for particles/projectiles, so effects can
  fade independently of the tracked skeleton.

## Known limitations

- MediaPipe libraries are loaded from a public CDN; there is no offline
  fallback and no handling for a failed/slow script load.
- Camera or model-load failures only surface as a short status-line
  message — there is no persistent error log.
- Gesture thresholds are heuristic and tuned by eye; accuracy varies
  with lighting, hand size, and camera angle.
- Hand tracking loses its identity (Hand A / Hand B can swap) if a hand
  briefly leaves and re-enters the frame.

## License

Add a license of your choice (e.g. MIT) before publishing.
