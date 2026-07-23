# Changelog

## v4 (current, `index.html`) — vs v3

v3 was a CDN-only, always-on-blast build with no dropout handling.
v4 ("merged") adds a control layer, robustness, and real tracking
telemetry on top of the same core gesture engine.

### Reliability
- **CDN → local vendor**: `hands.js` / `camera_utils.js` now load from
  `./vendor/...` instead of `jsdelivr`, so the app works offline and
  isn't blocked by ad-blockers/CSP. See [`vendor/README.md`](./vendor/README.md).
- **On-screen error box**: surfaces MediaPipe load failures, camera
  permission errors, and a timeout if no detection callback fires
  within 8s — v3 failed silently.
- **Lower-latency model config**: `modelComplexity` `1 → 0` and
  slightly relaxed detection/tracking confidence thresholds (`0.6 →
  0.55/0.5`) for faster frames, at a small cost to landmark precision.

### New features
- **Neon control panel**: live sliders for line thickness, glow
  intensity, and particle density (`LINE_THICKNESS`, `GLOW_INTENSITY`,
  `PARTICLE_DENSITY`), all previously hardcoded in v3's draw calls.
- **Bicep curl rep counter**: tracks hand A's wrist Y-position with an
  adaptive min/max + hysteresis to count reps, with a HUD readout and
  reset button. Not in v3 at all.
- **Landmark smoothing (EMA)**: each hand's points are exponentially
  smoothed (`SMOOTHING_ALPHA = 0.55`) to cut jitter — v3 drew raw
  landmarks directly.
- **Hold/fade persistence**: a hand that briefly drops out of detection
  is held in place for 450ms then faded over 250ms, instead of
  vanishing instantly (v3's behavior), so two-hand effects don't
  flicker.
- **Technical HUD overlays** (new): per-hand corner-bracket target box
  showing the model's real detection confidence, a wrist velocity
  vector (speed + direction), and a live joint-angle readout at the
  index finger PIP — all reflecting actual tracking data, not just
  decoration. Plus a slow scan-line sweep for atmosphere.
- **Richer two-hand bridge**: v3 drew one dashed line between wrists.
  v4 connects each matching fingertip pair with a glowing beam with a
  traveling light glint, plus a thinner secondary dashed wrist link.

### Layout tweaks
- `themeHint` moved from bottom-left to bottom-right; `distReadout`
  shifted up slightly — both to make room for the new error box and
  control panel without overlapping.
