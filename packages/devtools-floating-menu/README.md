# @buoy-gg/core

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/core)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)

**Devtools that live in your app. And answer to your agent.**

Buoy is a floating dev menu that ships inside your React Native app. Every request, state change, render, and frame — live on the phone, on your desktop, and in Claude or Cursor. Drop in one component, install the tools you want, and they appear in the menu on their own.

![Buoy demo — the floating dev menu in action](https://github.com/user-attachments/assets/a732d6a3-9963-49e3-b0f1-0d974a0a74d7)

## Install

```bash
npm install @buoy-gg/core
```

## Quick start

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* Your app */}
      <FloatingDevTools />
    </>
  );
}
```

That's the whole setup — a floating dev menu appears inside your app. Install any tool package and it auto-appears in the menu; most need zero config, a few take one line (like passing `zustandStores` or calling `watchAtoms()`). Add a `licenseKey` prop to unlock [Pro](https://buoy.gg/pricing): production builds, the MCP server, and unlimited capture.

## The 15 tools

| Tool | Package | What it does |
| --- | --- | --- |
| [Network](https://buoy.gg/buoy/latest/docs/tools/network) | `@buoy-gg/network` | Every request, response, timing & error — GraphQL operations extracted and named |
| [Storage](https://buoy.gg/buoy/latest/docs/tools/storage) | `@buoy-gg/storage` | Browse & edit AsyncStorage, MMKV & SecureStore with change history |
| [Env](https://buoy.gg/buoy/latest/docs/tools/env) | `@buoy-gg/env` | Auto-discovers `EXPO_PUBLIC_` vars, validates types, scores config health |
| [Query](https://buoy.gg/buoy/latest/docs/tools/react-query) | `@buoy-gg/react-query` | TanStack Query cache — refetch, invalidate, simulate loading & error states |
| [Routes](https://buoy.gg/buoy/latest/docs/tools/routes) | `@buoy-gg/route-events` | Navigation events, the live stack, and the full sitemap — jump to any screen |
| [Debug Borders](https://buoy.gg/buoy/latest/docs/tools/debug-borders) | `@buoy-gg/debug-borders` | Depth-colored layout borders — tap any label for testID, a11y props & styles |
| [Highlight Updates](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) | `@buoy-gg/highlight-updates` | Every render, with the cause: mount, state, props, or parent |
| [Bench](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) | `@buoy-gg/perf-monitor` | UI/JS FPS, CPU, memory & jank on a real device — batch benchmarks, ranked reports |
| [Events](https://buoy.gg/buoy/latest/docs/tools/events) | `@buoy-gg/events` | One timeline across every tool, with LLM-friendly export presets |
| [Console](https://buoy.gg/buoy/latest/docs/tools/console) | `@buoy-gg/console` | Chrome-DevTools console on device — read logs with no cable and no Metro |
| [Redux](https://buoy.gg/buoy/latest/docs/tools/redux) | `@buoy-gg/redux` | Inspect, dispatch & time travel — flags actions that blow the frame budget |
| [Zustand](https://buoy.gg/buoy/latest/docs/tools/zustand) | `@buoy-gg/zustand` | State, diffs, jump-to-state, one-tap reset — no middleware needed |
| [Jotai](https://buoy.gg/buoy/latest/docs/tools/jotai) | `@buoy-gg/jotai` | One `watchAtoms()` call — every event shows `prev → next` |
| [Impersonate](https://buoy.gg/buoy/latest/docs/tools/impersonate) | `@buoy-gg/impersonate` | Switch users, roles & flags without rebuilding |
| [Overlay](https://buoy.gg/buoy/latest/docs/tools/image-overlay) | `@buoy-gg/image-overlay` | Pin Figma mockups pixel-perfect over the app |

Grab everything in one line:

```bash
npm i @buoy-gg/{core,network,storage,env,react-query,route-events,debug-borders,highlight-updates,perf-monitor,events,console,redux,zustand,jotai,impersonate,image-overlay}
```

## Three surfaces, one live session

Every tool runs inside your app's process. The phone, the desktop, and your agent all see the same session, live.

- **On the phone** — tap the floating menu. Works on any device, no cable, no desktop app, no Metro.
- **On your desktop** — [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free) mirrors the tools to full-screen panels on macOS, Windows & Linux, with a live FPS / CPU / memory HUD and remote control of the device. Devices connect automatically — physical phones included, no URLs to configure.
- **Through your agent** — the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp) gives Claude Code, Cursor, or any MCP editor structured tool calls into the running app: read live state, tap real buttons, run benchmarks on a physical device.

## Free vs Pro

Every tool is **free** — no key, no signup, no time limit.

**Pro** unlocks production builds, the MCP server, and unlimited capture. Activate with one prop:

```tsx
<FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
```

**Weekend Pass:** every Saturday and Sunday, every Pro feature unlocks free for everyone — built into the product, not a promo. Details at [buoy.gg/pricing](https://buoy.gg/pricing).

## Safe in production

Everything runs in your app's process. The broker that mirrors the session to desktop and MCP binds to localhost only — nothing ever leaves your machine. You decide who sees the tools:

```tsx
{isInternalUser && <FloatingDevTools />}
```

That one line is the whole production story: internal builds and flagged users get the full toolkit; everyone else gets nothing.

Need the session with **no on-device UI at all** — field builds or devices handed to non-developers? Headless mode keeps every tool syncing to Buoy Desktop and MCP while rendering nothing on the device:

```tsx
<FloatingDevTools headless />
```

## Links

- [Full documentation](https://buoy.gg/buoy/latest/docs/overview)
- [GitHub](https://github.com/Buoy-gg/buoy)
- [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop)
- [Pricing](https://buoy.gg/pricing)

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).
