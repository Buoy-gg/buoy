# @buoy-gg/debug-borders

[![npm version](https://img.shields.io/npm/v/@buoy-gg/debug-borders?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/debug-borders) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/debug-borders?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/debug-borders)

**See your React Native layout instead of guessing at it — depth-colored borders over every component, and tap any label to inspect its testID, styles, and exact position on-device.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Debug Borders demo](https://github.com/user-attachments/assets/945fdb5d-2546-442d-98e7-ef73231abbba)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/debug-borders
```

## Quick start

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

Tap the BORDERS icon in the floating menu to cycle Off → Borders → Labels.

Want your own naming or colors? Build the tool with `createDebugBordersTool({ name: "LAYOUT", bordersColor: "#ec4899", ... })` and pass it to `FloatingDevTools` via the `apps` array.

## What you get

- **Depth-colored borders** — rainbow borders on every component, colored by depth in the tree, fully touch-through so they never block interaction.
- **Labels mode** — filters down to components with a `testID` or `accessibilityLabel`: green = testID, pink = accessibilityLabel. Labels sit above their boxes and stack automatically when they overlap.
- **Tap to inspect** — tap any label for the component's `testID`, `accessibilityLabel`, `nativeID`, key, accessibility props, full computed styles, and exact position and size.
- **Answers real questions on-device** — "what testID does QA need for this button?" or "which component owns this padding?" without leaving the phone.
- **Stays out of the way** — borders auto-hide on inactive screens in stack navigators and whenever a DevTools modal is open, and they track layout changes automatically.
- **Works everywhere** — Expo and React Native CLI, Fabric (New Architecture) and Paper.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/debug-borders) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
