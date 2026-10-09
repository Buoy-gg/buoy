---
title: Scene (Beta)
description: See and edit a three.js scene.
---

# Scene (Beta)

See the tree of objects in your 3D app. Pick a node to draw a box around it. Hide it, move it, turn it, or change its size. You can change a mesh's color too.

::three-demo

## Set it up

Three.js support is in Beta. Use the `beta` tag once it ships.
Keep all Buoy packages on the same beta release.
Install `@buoy-gg/three@beta` with `three` in your web app. Add its module to Buoy's `modules` map.

```tsx
import * as threeTools from '@buoy-gg/three/web';
import * as perfMonitor from '@buoy-gg/perf-monitor/web';
import * as assets from '@buoy-gg/assets/web';
import * as events from '@buoy-gg/events/web';
import { registerThree } from '@buoy-gg/three/web';

const modules = { three: threeTools, 'perf-monitor': perfMonitor, assets, events };
// Add these modules to your FloatingDevTools or mountBuoy options.
const stop = registerThree({ renderer, scene, camera, loadingManager });
// Call stop() when the scene is replaced or torn down.
```

Call `stop()` on hot reload too. Each renderer has its own group. In R3F, call this in an effect with `useThree()`. Pass `gl` as `renderer`. Return `stop` from the effect.

The early boot hook can find scenes and renderers. It cannot tell which ones belong together. Use `registerThree` to link them. Load `@buoy-gg/core/web/register` before three.js, as shown in the [web guide](../web/three.md).

## Counts and loads

Perf Monitor shows counts after the app renders. WebGL and WebGPU have different fields. A field with no data shows as unknown. Counts are objects, not bytes. A count that keeps rising says "Keeps growing". A cache can cause this too.

Buoy does not reset counts or change `autoReset`. With many render passes, counts follow your reset rules. With no new render, the last counts stay on screen.

Assets and Events show file loads. Buoy watches `DefaultLoadingManager`. Pass a custom manager to `registerThree` to watch it too. Size and time come from the browser's resource timing. If that data is absent, they stay unknown. Cache hits show when the cache or browser proves a hit.

## Scene edits

Each tree has at most 500 nodes. Long names are cut short. The panel tells you when it hits the node limit. Turn values use radians. Enter three numbers for a move, turn, or size. Colors use six digit hex, such as `#ff8800`.

Each field needs a value. Live changes keep your draft until you press Save.

A box marks the picked node's bounds at the time you pick it. Clear it with "Clear box". App code may overwrite an edit on its next frame.

Desktop uses this same panel. MCP has `get_three_scene`, `get_three_stats`, and `three_action`. MCP needs a paid account. Read the scene first to get IDs for edits.

Workers and WebXR are not supported. For full screen, use a wrapper around the canvas. A bare canvas cannot show Buoy's DOM panel.
