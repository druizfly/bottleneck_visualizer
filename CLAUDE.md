# Bottleneck Visualization

## What this is

A single-file interactive particle animation that visualizes flow bottlenecks in any pipeline. Originally reverse-engineered from the canvas-based visualization on [background-agents.com](https://background-agents.com) (the "flowrate" component by Ona/Lou Jaybee), then generalized into a configurable template.

## How it works

Particles spawn on the left edge and drift rightward through labeled columns. Each column is either **normal** (speed 0.7 — particles crawl and accumulate) or **accelerated** (speed 5 — particles zip through, colored red). The visual effect: dots pile up *before* fast stages, making bottlenecks immediately obvious.

The entire app lives in `index.html` — no build step, no dependencies, no framework.

## File structure

```
index.html   — Full app: config UI + canvas particle engine
CLAUDE.md    — This file
```

## Architecture (single file, two sections)

### 1. Configuration UI (DOM)
- Renders a list of stage rows, each with: drag handle, text input, speed toggle (Normal / Accelerated), remove button
- Drag-and-drop reorder via native HTML5 drag events
- "Add stage" appends a new row; "Apply" calls `initViz()` to restart the animation
- Minimum 2 stages enforced

### 2. Particle engine (Canvas 2D)
- Uses `requestAnimationFrame` loop at native refresh rate
- HiDPI-aware: scales canvas by `devicePixelRatio`
- Responsive: recalculates width on resize

Key constants (tune these for different feels):
| Constant | Value | Purpose |
|---|---|---|
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
| `CANVAS_H` | 180px | Fixed canvas height |

### Mouse/touch interaction
Hovering pushes nearby particles away (radial repulsion). Each particle has a `baseY` it springs back to, creating a satisfying wobble effect.

### Color scheme
- Accent (accelerated dots + labels): `#C41E3A` (crimson)
- Normal dots: `#B0B0B0` at 50% opacity
- Accelerated dots: `#C41E3A` at 90% opacity
- Background: `#FFFFFF` with subtle `rgba(0,0,0,0.015)` fill for depth
- UI chrome: Inter font, `#FAFAFA` page bg, `#E8E8E8` borders

## Origin / attribution

The particle animation logic was extracted from the minified source of `background-agents.com`:
- Main page bundle: `landscape-BdxirnYE.js`
- Visualization component: `WhatsNewPill-BaNGgpzN.js` (export `a` / internal name `bt`)
- Created by Lou Jaybee and the team at [Ona](https://ona.com)

The original rendered a fixed CODE → REVIEW → TEST pipeline with white background. This version generalizes it to accept any ordered list of stages with per-stage speed control.

## Future iteration ideas

- URL hash or query-param config so visualizations can be shared as links
- Export as GIF or video
- Dark mode theme toggle
- Multiple speed tiers (slow / normal / fast / instant) instead of binary
- Adjustable particle density and spawn rate from the UI
- Embed mode (hide config panel, just show canvas) for use in presentations
- Per-stage custom colors
- Particle trail / motion blur effect
- Pause / step-through mode for presentations
