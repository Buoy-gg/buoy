<div align="center">

# 🛟 Buoy

**Devtools that live in your app. And answer to your agent.**

[Docs](https://buoy.gg/buoy/latest/docs/overview) · [Quick Start](#-quick-start) · [Desktop](#%EF%B8%8F-buoy-desktop) · [MCP](#-your-agent-gets-hands) · [Pricing](https://buoy.gg/pricing)

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/core)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)
[![legacy downloads](https://img.shields.io/npm/dt/react-native-react-query-devtools?style=flat-square&labelColor=1c1c1c&color=10B981&label=legacy%20downloads)](https://www.npmjs.com/package/react-native-react-query-devtools)
[![TypeScript](https://img.shields.io/badge/TypeScript-100%25-10B981?style=flat-square&labelColor=1c1c1c)](https://www.typescriptlang.org/)

Buoy is a floating dev menu that ships inside your React Native app — every request, state change, render, and frame, live on the phone, on your desktop, and in Claude or Cursor.

**Every tool is free. Pro unlocks production builds, MCP & unlimited capture.**

**🎯 Now in beta: [Buoy for Flutter](https://github.com/Buoy-gg/Buoy-Flutter)** — `flutter pub add buoy`. The network inspector ships first, streaming to the same desktop dashboard and MCP server. [Vote for the next Flutter tool →](https://buoy.gg/roadmap)

![Buoy demo — the floating dev menu in action](https://github.com/user-attachments/assets/a732d6a3-9963-49e3-b0f1-0d974a0a74d7)

</div>

- **One component, zero config** — drop in `<FloatingDevTools />` once; install any tool package and it appears in the menu on its own
- **15 tools, every environment** — network, state, storage, renders, performance and more, in dev, staging, *and* production builds
- **Your agent can drive it** — Claude or Cursor reads live state, taps real buttons, and benchmarks on a physical device over MCP

---

## ⚡ Quick Start

```bash
npm install @buoy-gg/core
```

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

That's the whole setup. A floating dev menu appears inside your app.

> [!NOTE]
> Install any tool package and it auto-appears in the menu. Most tools need zero config — a few take one line, like passing `zustandStores` or calling `watchAtoms()`. Every tool is free, no key or signup needed. Add a `licenseKey` to unlock [Pro](https://buoy.gg/pricing): production builds, the MCP server, and unlimited capture.

---

## 🛟 One live session. Three ways in.

Every tool runs inside your app's process. The phone, the desktop, and your agent all see the same session, live.

- **📱 On the phone** — tap the floating menu. Works on any device, no cable, no desktop app, no Metro.
- **🖥️ On your desktop** — [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) mirrors the on-device tools to full-screen panels on macOS, Windows & Linux, with a live UI/JS FPS · CPU · memory HUD and remote control of the device.
- **🤖 Through your agent** — the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp) gives Claude Code, Cursor, or any MCP editor structured tool calls into the running app.

---

## 🧰 The 15 tools

| Tool | Package | What it does |
| --- | --- | --- |
| [Network](https://buoy.gg/buoy/latest/docs/tools/network) | `@buoy-gg/network` | Every request, response, timing & error — GraphQL operations extracted and shown as `GetUser › 123` |
| [Storage](https://buoy.gg/buoy/latest/docs/tools/storage) | `@buoy-gg/storage` | Browse & edit AsyncStorage, MMKV & SecureStore with change history — biometric keys listed, never auto-read |
| [Env](https://buoy.gg/buoy/latest/docs/tools/env) | `@buoy-gg/env` | Auto-discovers `EXPO_PUBLIC_` vars, validates types, scores config health 0–100% |
| [Query](https://buoy.gg/buoy/latest/docs/tools/react-query) | `@buoy-gg/react-query` | TanStack Query cache — refetch, invalidate, simulate loading & error states, one-tap offline |
| [Routes](https://buoy.gg/buoy/latest/docs/tools/routes) | `@buoy-gg/route-events` | Navigation events, the live stack, and the full sitemap — jump to any screen |
| [Debug Borders](https://buoy.gg/buoy/latest/docs/tools/debug-borders) | `@buoy-gg/debug-borders` | Depth-colored layout borders (touch-through) — tap any label for testID, a11y props & computed styles |
| [Highlight Updates](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) | `@buoy-gg/highlight-updates` | Every render, with the cause: mount, state, props, or parent — down to the exact `useState` before → after |
| [Bench](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) | `@buoy-gg/perf-monitor` | UI/JS FPS, CPU, memory & jank on a real device — batch benchmarks return a ranked report |
| [JS Top](https://buoy.gg/buoy/latest/docs/tools/js-top) | `@buoy-gg/js-top` | Task Manager for the JS thread — a live ranked table of which timers, Promise chains & callbacks eat your JS FPS |
| [Images](https://buoy.gg/buoy/latest/docs/tools/images) | `@buoy-gg/images` | Every image load with cache verdict (memory/disk/network), timing, oversize & wasted-memory audit, and failure diagnosis |
| [Events](https://buoy.gg/buoy/latest/docs/tools/events) | `@buoy-gg/events` | One timeline across every tool, with LLM export presets: Markdown for agents, Bug Report, Errors Only |
| [Console](https://buoy.gg/buoy/latest/docs/tools/console) | `@buoy-gg/console` | Chrome-DevTools console on device — read logs from a release build with no cable and no Metro |
| [Redux](https://buoy.gg/buoy/latest/docs/tools/redux) | `@buoy-gg/redux` | Inspect, dispatch & time travel — flags actions that blow the 16ms frame budget |
| [Zustand](https://buoy.gg/buoy/latest/docs/tools/zustand) | `@buoy-gg/zustand` | State, diffs, jump-to-state, one-tap reset — no middleware needed |
| [Jotai](https://buoy.gg/buoy/latest/docs/tools/jotai) | `@buoy-gg/jotai` | One `watchAtoms()` call, no wrappers — every event shows `prev → next`, even writes that changed nothing |
| [Impersonate](https://buoy.gg/buoy/latest/docs/tools/impersonate) | `@buoy-gg/impersonate` | Switch users, roles & flags without rebuilding — injects `x-impersonate-user-id` into every request |
| [Overlay](https://buoy.gg/buoy/latest/docs/tools/image-overlay) | `@buoy-gg/image-overlay` | Pin Figma mockups pixel-perfect over the app — Component Mode tracks a tagged component through scroll & animation |

Grab everything in one line:

```bash
npm i @buoy-gg/{core,network,storage,env,react-query,route-events,debug-borders,highlight-updates,perf-monitor,js-top,images,events,console,redux,zustand,jotai,impersonate,image-overlay}
```

<details>
<summary><strong>See them in action</strong></summary>

<p align="center">
  <img src="https://github.com/user-attachments/assets/473ddf83-03cd-4bd1-8dc3-0f66eda9fa8a" width="30%" alt="Network tool" />
  <img src="https://github.com/user-attachments/assets/80ef1c60-d20c-4d8b-97e6-f37b21b315ea" width="30%" alt="Storage tool" />
  <img src="https://github.com/user-attachments/assets/258e892d-3eaf-41f8-9fae-d7d2dcd6c39d" width="30%" alt="React Query tool" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/90e55dc7-f8ab-423a-9770-84b9ff9c8446" width="30%" alt="Routes tool" />
  <img src="https://github.com/user-attachments/assets/945fdb5d-2546-442d-98e7-ef73231abbba" width="30%" alt="Debug Borders tool" />
  <img src="https://github.com/user-attachments/assets/75651046-33a0-4257-9011-3bcc4818a964" width="30%" alt="Env tool" />
</p>

</details>

---

## 🤖 Your agent gets hands

```bash
npx -y @buoy-gg/mcp@latest init
```

One command wires the Buoy MCP server into Claude Code, Cursor, and VS Code — non-destructively.

**Your agent presses real UI handlers in JS, reads live app state, and benchmarks on a physical device — no screenshots, no pixel coordinates, no synthesized touches.**

- **Read the runtime** — `get_events` and `get_console` stream what the app is actually doing, in token-friendly summaries
- **Drive the UI** — `describe_screen` walks the live React tree into an accessibility-style element list with tap points; `tap_element` presses the real handler in JS. Works on physical devices.
- **Change state** — `get_redux_state` / `redux_dispatch`, `get_zustand_state`, `get_jotai_state`, `get_react_query` / `react_query_action`, `get_storage` / `storage_action`, `get_routes` / `navigate`
- **Measure** — `run_benchmark_batch` runs Bench cases on a real device and returns a ranked comparison; `screenshot_component` returns a tight crop of any component by testID (iOS Simulator); `list_devices` picks the target

`init` also installs the **buoy-optimize** skill: a wizard that benchmarks variants on the real device, applies the winner, and repeats until metrics plateau. One real run took a Skia LED display from 28 stuttering lights to over 12,000 — no lag.

📚 [AI / MCP docs](https://buoy.gg/buoy/latest/docs/mcp) · Pro feature

---

## 🖥️ Buoy Desktop

Every tool, full screen. [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) is free for macOS, Windows & Linux:

- Zero-config connect — devices find your machine automatically, physical phones included
- Full-screen panels for Network, Console, Storage, and every state tool
- Live UI/JS FPS · CPU · memory HUD
- Remote actions — dispatch, navigate, invalidate, from the keyboard
- Multi-device switching and a component screenshot tool
- Themes with animated glow, because you'll be staring at it all day

📚 [Desktop docs](https://buoy.gg/buoy/latest/docs/desktop)

---

## Why Buoy?

Flipper is deprecated. What's left is shaking the device for a dev menu, `console.log` archaeology over a cable, and desktop debuggers that stop working the moment you ship a staging or production build. And when an AI agent works on your app, it's debugging blind — it can edit the code but can't see what the running app is doing.

Buoy inverts the model: the tools live in the app itself, so they go wherever the app goes — the simulator, a tester's phone, production. A tiny localhost broker mirrors the same session to your desktop and your agent.

|                              | Buoy | Flipper | Reactotron | RN Debugger |
| ---------------------------- | :--: | :-----: | :--------: | :---------: |
| On-device, no desktop app required | ✅ | ❌ | ❌ | ❌ |
| Works in production builds   | ✅   | ❌      | ❌         | ❌          |
| AI agent control (MCP)       | ✅   | ❌      | ❌         | ❌          |
| QA & support can use it      | ✅   | ❌      | ❌         | ❌          |
| Zero config setup            | ✅   | ❌      | ❌         | ❌          |

---

## Safe in production

> [!IMPORTANT]
> Everything runs in your app's process. The broker that mirrors to desktop and MCP binds to localhost only — nothing ever leaves your machine. You decide who sees the tools:

```tsx
<>
  {/* Your app */}
  {isInternalUser && <FloatingDevTools />}
</>
```

That one line is the whole production story: internal builds and flagged users get the full toolkit; everyone else gets nothing.

Want the session without any on-device UI at all — field builds, kiosk devices, apps handed to non-developers? Headless mode keeps every tool syncing to Buoy Desktop and MCP while rendering nothing on the device:

```tsx
<FloatingDevTools headless />
```

---

## 💳 Every tool is free. Pro unlocks the rest.

Every tool is **free** — no key, no signup, no time limit.

**Pro** unlocks production builds, the MCP server, and unlimited capture — **$29/seat/month or $290/year**, with a 14-day trial. Activate with one prop:

```tsx
<FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
```

**Weekend Pass:** every Saturday and Sunday, every Pro feature inside the tools unlocks free for everyone. Not a promo — it's built into the product. Try it for real, decide on Monday.

➡️ [buoy.gg/pricing](https://buoy.gg/pricing)

---

## Feedback

Found a bug or want a tool that doesn't exist yet? [Open an issue](https://github.com/Buoy-gg/buoy/issues) — feature requests drive the roadmap.

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).

---

<p align="center"><sub>You read the whole README. Pro unlocks free every weekend — see you Saturday. 🛟</sub></p>
