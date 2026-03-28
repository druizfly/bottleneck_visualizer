# Flow Bottleneck Visualizer

An interactive particle animation that makes pipeline bottlenecks immediately obvious. Configure any sequence of stages, set their speed and relative length, and watch particles pile up to reveal where flow gets stuck.

**[Live demo →](https://druizfly.github.io/bottleneck_visualizer/)**

---

## How it works

Particles spawn on the left edge and drift rightward through labeled columns. Each column has a **speed** (how fast particles move through it) and a **length** (how wide it appears in the visualization). The visual effect: dots accumulate *before* fast stages and spread out inside slow ones — making bottlenecks jump out at a glance.

No build step. No dependencies. No framework. The entire app is a single `index.html`.

---

## User guide

### Configuring stages

Each row in the panel represents one stage in your pipeline.

| Control | What it does |
|---|---|
| **⠿ drag handle** | Drag rows to reorder stages |
| **Stage name** | Free-text label (shown in the visualization) |
| **Speed badge** | Cycles through Normal → Accelerated → Slow |
| **− / + (Length)** | Sets the relative width of the column (1–9) |
| **×** | Removes the stage (minimum 2 stages) |

Click **Apply** to restart the visualization with your current settings.

### Speed modes

| Mode | Color | Particle speed | When to use |
|---|---|---|---|
| **Normal** | Gray | Medium | Typical stages |
| **Accelerated** | Red | Very fast | Automated / instant stages |
| **Slow** | Blue | Very slow | Bottlenecked / high-friction stages |

Particles pile up **before** fast stages — that's where your bottleneck is. Slow stages turn blue and particles spread out inside them.

### Length

Use **−** and **+** to set the relative effort or duration of each stage. A stage with length 3 gets three times as much canvas space as one with length 1. This lets you accurately represent that coding takes longer than deploying.

### Sharing a visualization

Click **Share link** to encode your current configuration into the URL and copy it to clipboard. Anyone opening that link will see the exact same pipeline — no server required.

Example shared link:
```
https://druizfly.github.io/bottleneck_visualizer/#PLAN,n,1|CODE,f,3|REVIEW,s,2|TEST,n,2|DEPLOY,n,1
```

### Mouse interaction

Hover over the canvas to push particles away. They spring back to their baseline, creating a satisfying wobble effect. Works on touch screens too.

---

## Examples

**Software delivery pipeline**
```
PLAN (Normal, 1) → CODE (Accelerated, 3) → REVIEW (Slow, 2) → TEST (Normal, 2) → DEPLOY (Normal, 1)
```

**Product discovery flow**
```
NEW IDEA (Slow, 1) → DISCOVERY (Accelerated, 3) → PLAN (Accelerated, 1) → REFINEMENT (Slow, 2) → DEVELOPMENT (Normal, 5) → QA (Normal, 1) → RELEASE (Normal, 1)
```

---

## Origin / attribution

The particle animation logic was reverse-engineered from the canvas visualization on [background-agents.com](https://background-agents.com), created by Lou Jaybee and the team at [Ona](https://ona.com). The original rendered a fixed CODE → REVIEW → TEST pipeline. This version generalizes it to accept any pipeline configuration.
