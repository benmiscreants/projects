# Dither — Image & Text Bitmap Converter

`dither.html` — single-file browser tool, no dependencies, no build step.

## What it does

Converts images or typed text into 1-bit (black/white) bitmap art using classic dithering algorithms. Results can be exported as SVG or PNG.

## Modes

### Image mode
Drop or browse any image file. The original and dithered versions are shown side by side.

### Text mode
Type text and render it using a Google Font. Controls:
- **Font** — 21 Google Fonts across Serif, Sans-serif, Display, Monospace, and Script categories. Fonts load on demand; a dot indicator shows loading/ready state.
- **Weight** — updates dynamically to only show weights available for the selected font.
- **Size** — font size in pixels (16–400).
- **Tracking** — letter spacing (-10 to +60px).
- **Leading** — line height as a percentage (70–200%).
- **Padding** — canvas margin around the text.

Multi-line text works — press Enter in the text box.

## Dithering algorithms

### Error diffusion
Quantizes each pixel and propagates the rounding error to neighbouring pixels.

| Algorithm | Character |
|---|---|
| Floyd-Steinberg | Sharp, detailed, naturalistic. The classic. |
| Atkinson | Spreads only 75% of error — high contrast, retains highlights. Apple origin. |
| Jarvis-Judice-Ninke | 12-neighbour spread, smoother gradients. |
| Stucki | Jarvis variant with adjusted weights, slightly sharper. |
| Burkes | Two-row simplified Jarvis, fast with no ringing. |
| Sierra | Three-row, balanced between sharp and smooth. |

### Ordered (Bayer)
Compares luminance against a repeating threshold matrix. Produces regular, tiled patterns.

| Algorithm | Character |
|---|---|
| Bayer 2×2 | Tight crosshatch, minimal. |
| Bayer 4×4 | Classic screen-door halftone. |
| Bayer 8×8 | Smooth gradients, visible grid structure. |

### Simple
| Algorithm | Character |
|---|---|
| Threshold | Hard cutoff at the threshold value. Stark, posterized. |
| Random Noise | Threshold plus uniform random offset. Grainy, organic. |

## Adjustments

| Control | Effect |
|---|---|
| Threshold | Luminance cutoff point (0–255). Lower = more white, higher = more black. |
| Brightness | Shifts luminance before dithering. |
| Contrast | Scales luminance around the midpoint before dithering. |
| Pixelate | Downscales to 1/Npx resolution and upscales back with nearest-neighbour before dithering (1–32px). Creates a mosaic/block pixel effect. |

## Display scale
Zoom the preview without affecting the output dimensions: 0.25×, 0.5×, 1×, 2×, 4×.

## Export

- **SVG** — run-length encodes each row of black pixels into `<rect>` elements. Far more compact than one rect per pixel. Scales infinitely.
- **PNG** — exports the dithered canvas directly at native resolution.

## File structure

Everything is in `dither.html`. Sections in order:
1. CSS styles
2. HTML — sidebar (controls) + canvas area
3. JavaScript:
   - Google Fonts catalogue and dynamic loader
   - Element references and state variables
   - Algorithm data (Bayer matrices, error diffusion kernels, descriptions)
   - Core helpers (`luminance`, `adjust`)
   - `runDither()` — pixelation pre-processing + dither engine
   - `paintOutput()` — writes result to output canvas, updates stats
   - `renderText()` — measures and draws text to source canvas, then calls `runDither`
   - Export functions (`exportSVG`, `exportPNG`)
   - Event listeners
