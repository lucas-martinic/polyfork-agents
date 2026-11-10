---
name: polyfork
description: Find, remix and place free low-poly 3D models (props, buildings, vehicles, characters, animals, terrain, whole themed kits) from polyfork.dev. Use when the user needs 3D assets or a 3D scene, level, diorama or game world for three.js, React Three Fiber, Unity, Godot, Unreal, Blender, WebXR or the web, or asks for "a model of X", "low poly", "game assets" or "a scene with...".
---

# Polyfork: free low-poly 3D models and kits

Polyfork (https://polyfork.dev) is a library of 3,000+ low-poly 3D models built to be
called by agents. Every model states its real-world size in metres, its palette and its
triangle count, and most are **programs with sliders**: one `createAsset({...knobs})`
call gives a different shape, colourway, season or set of parts. Every low poly model is
free. HD editions (high poly, PBR) are the paid Pro tier.

## How to reach it (pick the first that works)

1. **MCP tools** (`search_assets`, `get_asset`, `get_kit`, `find_matching`,
   `get_variant`, `preview_scene`, ...): if the `polyfork` MCP server is connected,
   use it. Anonymous: `https://polyfork.dev/mcp`. Signed in (OAuth):
   `https://polyfork.dev/mcp/account`.
2. **CLI**: `npx polyfork search <words>`, then `npx polyfork add <id>` (puts the GLB
   in `public/models`). `--json` for structured output. Nothing to install.
3. **REST**, no key needed to read: `GET https://polyfork.dev/api/assets?q=<words>`,
   `GET /api/assets/{id}`, `GET /api/kits`, `GET /api/kits/{id}`.

Before assembling a whole scene, fetch https://polyfork.dev/prompt.txt once. It is
the full working guide (about 5,000 tokens) and it is kept current.

## Rules that decide whether the result looks right

- **Never rescale a model to make it fit.** Sizes are real metres (`size_m`). Pick a
  differently sized model instead, or use the model's own size knob.
- **Place at y = 0.** Models rest on the ground with the origin at the bottom centre,
  facing +Z, Y-up. Wall fixtures and ground tiles carry a non-zero `origin_y` and are
  still placed at y = 0.
- **Prefer a kit for a scene.** Parts of a kit share one palette, scale and grid.
  `get_kit` (or `/api/kits/{id}`) returns the assembly contract and the few lines that
  build and light the whole place. Read it before placing anything from a kit.
- **Use `find_matching`** (`/api/assets/{id}/matching`) to choose what goes with a
  model. Do not compare hex palettes yourself.
- **Remix with knobs, not by editing meshes.** `get_variant` (or
  `/api/assets/{id}/variant?want=<words>`) turns plain words into a `createAsset()`
  call. The words that work are the knob names and option values in
  `https://polyfork.dev/cdn/{id}-params.json`.
- **Lighting:** do not use ACES tone mapping at exposure 1 (they come out a third
  darker). `get_kit` returns the rig the colours were authored under.
- **Characters** share one Mixamo-named skeleton. Free clips play on any of them in
  three lines with `https://polyfork.dev/cdn/clips.mjs`.
- **Show, don't paste:** `https://polyfork.dev/scene?ids=a,b,c` is a page the user can
  open and orbit. Hand that over instead of three.js the user cannot run.
- **When nothing fits, say so** and offer `report_need` (`POST /api/demand`). A
  vaguely similar model is worse than an honest no.

## Using a model

```js
// three.js, the parametric source (free, CORS-open, no key):
import { createAsset } from 'https://polyfork.dev/cdn/<id>.mjs';
scene.add(createAsset({ /* knobs from <id>-params.json */ }));

// Any engine: the GLB
// https://polyfork.dev/cdn/<id>.glb   (FBX, OBJ, USDZ on the asset page)
```

In React, Vite or Next, download the `.mjs` and import it as a local file (a bare
`https://` import fails under bundlers). In R3F: `<primitive object={useMemo(() =>
createAsset(props), [JSON.stringify(props)])} />`.

## Licence and credit

The mesh files of free models (GLB, FBX, OBJ, USD) are CC BY 4.0 or the Polyfork
licence, the user's choice. Under CC BY, credit:
`<Title> by Polyfork (https://polyfork.dev/asset/<id>), CC BY 4.0`. The `.mjs`
programs and HD editions are under the Polyfork licence only. Tell the user which
applies when you hand over files.

## Accounts

Reading and loading free models needs no account. A free account (email or Google)
raises the remix budget and unlocks per-model zips and whole-kit zips. Every response
carries an `access` object with the current tier and remaining budget: read it rather
than guessing, and tell the user where the line is before they hit it.
