# 🦦 Otter Floatilla

A relaxing physics sandbox where sea otters float on their backs, drift with the ocean current, and hold hands so they don't float apart.

Everything lives in one self-contained [`index.html`](index.html): HTML5 canvas, CSS and vanilla JavaScript, with [Matter.js](https://brm.io/matter-js/) 0.20.0 loaded from cdnjs for the 2D physics.

## Running it

Open `index.html` in any modern browser. The only external dependency is Matter.js from the CDN, so you need to be online.

If you prefer to serve it locally:

```bash
python -m http.server 8765
```

Then visit <http://localhost:8765>. The included `.claude/launch.json` runs this same server for the Claude desktop app's preview pane.

## How to play

| Action | What happens |
| --- | --- |
| **Drag** an otter | Pick it up and herd it around. A slow drag tows its whole raft along. |
| **Yank** an otter quickly | Breaks its grip so it separates from its neighbour. |
| **Double-click / double-tap** water | A new otter plops in from above with a splash. |
| **Double-click / double-tap** an otter | It does a happy little hop. |

### Control panel (top-left)

- **Stats**: number of otters, hands currently held, and the size of the biggest raft.
- **Reset**: starts over with a small raft of three plus a few drifters.
- **+ Otter**: drops a new otter somewhere random.
- **Show physics debug**: overlays collision outlines, hand sensors (blue = free left, green = free right, pink = holding, orange = cooling down), hand-holding springs, the current's vector field, the walls and an FPS readout.
- **Ocean current**: scales the current from 0× (still water) to 3×.

On screens narrower than 500 px the panel starts collapsed; tap **+** to expand it.

## How it works

- **Top-down water.** The ocean is seen from above. Matter.js simulates the water surface with in-plane gravity switched off, so otters drift, bump and raft in 2D. Invisible walls sit just past the screen edges, and a soft "shoreline" force nudges otters back from the edges.
- **Buoyancy.** Each otter also runs its own small vertical model, where `z` is its height above the water. Gravity pulls it down, a buoyancy spring pushes it up when it's submerged, and heavy water damping settles it. That's what makes otters bob on the swell, splash when dropped in, and lift a little when picked up. Height is drawn as scale plus shadow offset.
- **Ocean current.** A swirling, divergence-free flow field that slowly rotates over time, with a gentle rhythmic swell. The force is applied at each otter's head and tail, so differences in the flow turn them naturally. Faint white streaks on the water trace the flow.
- **Holding hands.** Each otter has a left and a right hand sensor point just outside its flanks. When a free left hand comes within 22 px of another otter's free right hand, a soft `Matter.Constraint` spring links them and a glowing heart burst floats up. Hands that are close but not touching reach toward each other and pull gently together, so rafts form on their own. If a spring is stretched too far (a brisk yank of roughly 540 px/s or more), the grip breaks and those hands wait a moment before grabbing again.
- **Fixed timestep.** Physics steps at a fixed 60 Hz, independent of the display's refresh rate. Rendering happens on every animation frame.

## Tweaking

All the tunable numbers are in the `CFG` object near the top of the script: otter size, hand-grab distance, spring stiffness, break threshold, current strength, buoyancy constants, spawn height, the otter cap (70) and more. Each value has a comment explaining what it does.

## Project layout

```
index.html           the whole toy: markup, styles and script
README.md            this file
.claude/launch.json  local preview server config for the Claude desktop app
```
