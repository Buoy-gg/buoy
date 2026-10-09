---
title: Three.js (Beta)
seoTitle: "Buoy for three.js and React Three Fiber (Beta)"
id: web-three
description: "Set up Scene in your 3D app. See counts, file loads, and known limits."
---

Three.js support is in Beta. Use it in web apps.
Buoy draws on the page, above your scene.
It works with plain three.js and React Three Fiber (R3F).
Start with a dev build. Check the limits below.

## Install

Use the `beta` tag once the beta ships.
Keep all Buoy packages on the same beta release.
Keep the React version your app and R3F use.
Plain apps need React too, for Buoy's own UI.

```sh
npm install -D @buoy-gg/core@beta @buoy-gg/three@beta @buoy-gg/perf-monitor@beta @buoy-gg/assets@beta @buoy-gg/events@beta @buoy-gg/external-sync@beta
npm install -D react react-dom react-native-web@^0.21
npm install three
```

Skip peers you have. Use three.js 0.170 or newer.
R3F apps also need `@react-three/fiber`.
Each Buoy tool uses its `/web` entry.
See [web setup](./installation) to add more tools.

## Load register first

Point your HTML entry at this small boot file:

```html
<script type="module" src="/src/boot.ts"></script>
```

```ts
// src/boot.ts
import '@buoy-gg/core/web/register';
void import('./app');
```

Use this boot file for both app types below.
The app import waits until the hook is set.
Load register before three.js and React DOM load.
Three.js sends scene events once. It won't send them again.
A late hook misses those first events.
The hook keeps a short list with weak refs.
Old scenes can still be freed from memory.

Use `registerThree` to link the scene, camera, and renderer.
The boot hook alone cannot match them up.
Call the returned cleanup when you swap a scene.
Call it on hot reload and app teardown too.

## Share the tool map

Both examples use this file:

```ts
// src/buoy.ts
import * as threeTools from '@buoy-gg/three/web';
import * as perfMonitor from '@buoy-gg/perf-monitor/web';
import * as assets from '@buoy-gg/assets/web';
import * as events from '@buoy-gg/events/web';
import * as externalSync from '@buoy-gg/external-sync/web';

export const modules = {
  three: threeTools,
  'perf-monitor': perfMonitor,
  assets,
  events,
  'external-sync': externalSync,
};
```

## React Three Fiber

This Vite example shows a small dev scene:

```tsx
// src/app.tsx
import { useEffect } from 'react';
import { createRoot } from 'react-dom/client';
import { Canvas, useThree } from '@react-three/fiber';
import { FloatingDevTools } from '@buoy-gg/core/web';
import { registerThree } from '@buoy-gg/three/web';
import { modules } from './buoy';

function SceneLink() {
  const { gl, scene, camera } = useThree();
  useEffect(() => {
    if (!import.meta.env.DEV) return;
    return registerThree({ renderer: gl, scene, camera });
  }, [gl, scene, camera]);
  return null;
}

function App() {
  return (
    <div id="scene-wrap" style={{ width: '100vw', height: '100vh' }}>
      <Canvas>
        <mesh>
          <boxGeometry />
          <meshBasicMaterial color="#ff8800" />
        </mesh>
        <SceneLink />
      </Canvas>
      {import.meta.env.DEV && (
        <FloatingDevTools modules={modules} signIn />
      )}
    </div>
  );
}

const root = createRoot(document.getElementById('root')!);
root.render(<App />);
if (import.meta.hot) import.meta.hot.dispose(() => root.unmount());
```

Add `<div id="root"></div>` to your HTML.
Keep Buoy beside Canvas. Canvas cannot draw Buoy's UI.
Keep Buoy inside your Query and Redux providers.
Pass your Zustand stores and Jotai atoms as usual.
Buoy cannot read R3F's private store.

## Plain three.js with mountBuoy

Your app code does not need JSX:

```ts
// src/app.ts
import { Scene, PerspectiveCamera, WebGLRenderer, Mesh,
  BoxGeometry, MeshBasicMaterial } from 'three';
import { mountBuoy } from '@buoy-gg/core/web';
import { registerThree } from '@buoy-gg/three/web';
import { modules } from './buoy';

const wrap = document.getElementById('scene-wrap')!;
const renderer = new WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
wrap.appendChild(renderer.domElement);
const scene = new Scene();
const camera = new PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
camera.position.z = 3;
const geometry = new BoxGeometry();
const material = new MeshBasicMaterial({ color: '#ff8800' });
scene.add(new Mesh(geometry, material));

const stopScene = import.meta.env.DEV
  ? registerThree({ renderer, scene, camera }) : () => {};
const unmount = import.meta.env.DEV
  ? mountBuoy({ modules, signIn: true }) : () => {};
renderer.setAnimationLoop(() => renderer.render(scene, camera));

function stop() {
  stopScene();
  unmount();
  renderer.setAnimationLoop(null);
  geometry.dispose();
  material.dispose();
  renderer.dispose();
  renderer.domElement.remove();
}
if (import.meta.hot) import.meta.hot.dispose(stop);
// Call stop() when your app shuts down too.
```

Add `<div id="scene-wrap"></div>` to your HTML.
`mountBuoy` takes the same props as `FloatingDevTools`.
Call its return value to remove Buoy's root.
That root cannot read your app's React providers.
Query, Redux, and Highlight have no app to watch here.
Zustand can watch a vanilla store you pass in.

These examples gate the UI in dev builds.
They still import tools at the top of each file.
To keep them out of your public bundle, load them lazily.
See [build rules](./frameworks#production-builds) for release access.

## Full screen and pointer lock

Put the canvas in a full screen wrapper:

```ts
// Run from a button click.
await document.getElementById('scene-wrap')?.requestFullscreen();
```

Buoy moves into the wrapper, then back on exit.
A bare canvas in full screen cannot show Buoy.
Its child DOM is fallback content.
Press Esc to free a mouse held by pointer lock.
Then click Buoy to open a tool.

Buoy fields stop new game keys. Panels stop wheel events.
Buoy sends key-up events to clear held game keys.
Games that ignore sent events must clear their own keys.
Page handlers in the capture phase can still get input.

## What works in Beta

Scene shows a tree with up to 500 nodes.
Pick a node to draw a box around it.
Hide it, move it, turn it, or change its size.
You can change a mesh's color too.
App code may overwrite edits on the next frame.
See the [Scene tool](../tools/three) for fields and units.

Perf Monitor shows the last render counts.
Counts are objects, not GPU bytes.
Buoy does not reset counts or change `autoReset`.
WebGL and WebGPU have different fields. Missing fields stay unknown.
A real GPU test of WebGPU is still open.

Assets and Events show file loads from three.js.
Buoy watches `DefaultLoadingManager`.
Pass `loadingManager` to `registerThree` for a custom one.
Size and time need the browser's resource timing data.
When that data is absent, those fields stay unknown.
Network sees fetch and XHR. Image loads may use DOM images.
Use Assets to see those loads too.

Web tools can show logs, web calls, state, and routes.
State tools need their stores, atoms, or providers.
Highlight Updates and Debug Borders work on DOM nodes.
Use Scene for the objects drawn in 3D.
Images sees DOM images, not drawings in a canvas.

## Known limits

- WebXR headsets cannot show this DOM panel.
- Scenes in workers use a separate scope. Scene cannot see them.
- Bare-canvas full screen hides Buoy. Use a wrapper.
- Camera has no web build. Native stores need phone tools.
- Browser Push capture is not covered by this beta.
- Permissions has no plain browser adapter. Phone plugins are separate.
- Lifecycle does not change page visibility in these web tests.
- TV focus tools read DOM focus, not 3D nodes.
- A live Desktop check is still open for Scene.

## Desktop and MCP

Open Buoy Desktop and sign in. Pick your web tab.
The tool map above includes the Desktop link module.
See [web link setup](./installation#desktop-and-mcp) for hosts and ports.
Scene has a Desktop panel in this beta.
MCP needs a paid account and a linked app.

| MCP tool | Use |
| --- | --- |
| `get_three_scene` | Read groups and node IDs. Each tree has up to 500 nodes. |
| `get_three_stats` | Read last counts. Unknown fields are null. |
| `three_action` | Pick, clear, hide, move, turn, size, or tint a node. |

Read the scene first. Pass its `rendererId` for an action.
Pass the node `id` too, except for `clearSelection`.
Actions are `select`, `clearSelection`, `visible`, `position`, `rotation`, `scale`, and `color`.
Turns use radians. Moves, turns, and sizes take three numbers.
Use a boolean for `visible` and six digit hex for `color`.
Pick `deviceId` when more than one app is linked.
