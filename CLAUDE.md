# Bottleneck Visualization

## What this is

A single-file interactive particle animation that visualizes flow bottlenecks in any pipeline. Originally reverse-engineered from the canvas-based visualization on [background-agents.com](https://background-agents.com) (the "flowrate" component by Ona/Lou Jaybee), then generalized into a configurable template.

## How it works

Particles spawn on the left edge and drift rightward through labeled columns. Each column has a **speed** (Normal / Accelerated / Slow) and a **weight** (relative canvas width). Dots pile up *before* fast stages, making bottlenecks immediately obvious.

The entire app lives in `index.html` — no build step, no dependencies, no framework.

## File structure

```
index.html   — Full app: config UI + canvas particle engine
README.md    — User-facing documentation and guide
CLAUDE.md    — This file
```

## Architecture (single file, two sections)

### 1. Configuration UI (DOM)

- Renders a header row (Stage / Speed / Length) above a list of stage rows
- Each row: drag handle, text input, speed toggle, length stepper (− / +), remove button
- Drag-and-drop reorder via native HTML5 drag events
- "Add stage" appends a new row; "Apply" calls `initViz()` to restart the animation
- "Share link" encodes the current config into the URL hash and copies to clipboard
- Minimum 2 stages enforced

### 2. Particle engine (Canvas 2D)

- Uses `requestAnimationFrame` loop at native refresh rate
- HiDPI-aware: scales canvas by `devicePixelRatio`
- Responsive: recalculates width and column offsets on resize
- Column widths are **proportional to stage weight** (not equal) — computed via `buildOffsets()`
- `TOP_PAD` is dynamic: expands to 62px when any label needs diagonal rendering, 28px otherwise

Key constants (tune these for different feels):
| Constant | Value | Purpose |
|---|---|---|
| `SLOW_SPEED` | 0.15 | Pixels/frame for slow columns |
| `NORMAL_SPEED` | 0.7 | Pixels/frame for normal columns |
| `FAST_SPEED` | 5 | Pixels/frame for accelerated columns |
| `SPAWN_INITIAL` | 60 | Particles seeded on first render |
| `SPAWN_INTERVAL` | 200ms | Time between spawning new particles |
| `SPAWN_PER_TICK` | 2 | Particles added per spawn tick |
| `MIN_IN_FAST` | 4 | Minimum particles kept in each fast column |
| `MOUSE_RADIUS` | 80px | Hover interaction radius |
| `MOUSE_FORCE` | 1.2 | Repulsion strength on hover |
| `DAMPING` | 0.92 | Velocity damping per frame |
| `SPRING` | 0.02 | Spring constant pulling particles back to baseline Y |
| `CANVAS_H` | 220px | Fixed canvas height |

### Speed modes

| Mode | Color | Constant |
|---|---|---|
| Normal | Gray `#B0B0B0` | `NORMAL_SPEED` |
| Accelerated | Crimson `#C41E3A` | `FAST_SPEED` |
| Slow | Blue `#1A6BBF` | `SLOW_SPEED` |

### Label rendering

Labels are drawn in `TOP_PAD` zone above the particle area:
- **Horizontal** when the label fits the column width
- **Diagonal (45°)** when the column is too narrow — anchor X is clamped to prevent right-edge clipping
- Multi-word labels split across two lines; single words truncated at 10 chars with `…`

### URL hash serialisation

Format: `#LABEL,speedChar,weight|LABEL,speedChar,weight|...`
Speed chars: `n` = normal, `f` = fast, `s` = slow
`decodeStages()` runs `decodeURIComponent` on the full hash before splitting, so `%7C`-encoded pipes are handled correctly.

### Mouse/touch interaction

Hovering pushes nearby particles away (radial repulsion). Each particle has a `baseY` it springs back to, creating a satisfying wobble effect.

## Origin / attribution

The particle animation logic was extracted from the minified source of `background-agents.com`:
- Main page bundle: `landscape-BdxirnYE.js`
- Visualization component: `WhatsNewPill-BaNGgpzN.js` (export `a` / internal name `bt`)
- Created by Lou Jaybee and the team at [Ona](https://ona.com)

The original rendered a fixed CODE → REVIEW → TEST pipeline with white background. This version generalizes it to accept any ordered list of stages with per-stage speed and length control.

## Future iteration ideas

- Export as GIF or video
- Dark mode theme toggle
- Adjustable particle density and spawn rate from the UI
- Embed mode (hide config panel, just show canvas) for use in presentations
- Per-stage custom colors
- Particle trail / motion blur effect
- Pause / step-through mode for presentations
