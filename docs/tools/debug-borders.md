---
title: Debug Borders
id: tools-debug-borders
description: "Visualize your React Native layout with colored borders and labels — tap any element to inspect its testID, styles, position, and accessibility info on-device."
---

<!-- ::platform-badge platform="both" -->

See your layout instead of guessing at it. Debug Borders draws colored borders and labels over your running app, then lets you tap any label to inspect the component underneath — its `testID`, styles, position, and accessibility info — without leaving the device.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/debug-borders" yarn="yarn add @buoy-gg/debug-borders" pnpm="pnpm add @buoy-gg/debug-borders" bun="bun add @buoy-gg/debug-borders" -->

That's it. Debug Borders appears in your FloatingDevTools menu.

---

## Three Modes, One Tap

Tap the BORDERS icon in the floating menu to cycle through modes:

| Mode        | What you see                                                                 |
| ----------- | ---------------------------------------------------------------------------- |
| **Off**     | Nothing — zero overhead                                                       |
| **Borders** | Rainbow-colored borders on every component, colored by depth in the tree      |
| **Labels**  | Borders + labels, but only for components with a `testID` or `accessibilityLabel` |

Borders are touch-through — they never interfere with interacting with your app — and they track layout changes automatically.

---

## Labels Mode

Labels mode filters the noise down to components you've actually identified:

- **Color-coded by identifier** — green for `testID`, pink for `accessibilityLabel`
- **Labels sit above their boxes** so they don't obscure content, and overlapping labels stack upward automatically
- **Hidden screens stay clean** — borders auto-hide on inactive screens in stack navigators, and whenever a DevTools modal is open

---

## Tap to Inspect

Tap any label to open a full inspection modal for that component:

- **Identifiers** — `testID`, `accessibilityLabel`, `nativeID`, and `key`
- **Component info** — the React component that rendered it, its parent, display name, and native view type
- **Position & size** — x/y coordinates, width, height, and depth in the tree
- **Accessibility** — role, hint, and state
- **Styles** — the full computed styles in an interactive viewer

Perfect for answering "what testID does QA need for this button?" or "which component owns this padding?" straight from the device.

---

## Customization

Want your own naming or colors? Build the tool with `createDebugBordersTool`:

```tsx
import { createDebugBordersTool } from "@buoy-gg/debug-borders";

const layoutTool = createDebugBordersTool({
  name: "LAYOUT",
  description: "Layout visualizer",
  offColor: "#9ca3af",
  bordersColor: "#ec4899",
  labelsColor: "#8b5cf6",
  id: "custom-borders",
});
```

Pass it to `FloatingDevTools` via the `apps` array and it replaces the default preset.

---

## What's Next

- [Highlight Updates](./highlight-updates) — See why your components re-render
- [Image Overlay](./image-overlay) — Overlay design mockups on your running app
- [Bench](./perf-monitor) — Benchmark FPS, CPU, and memory on a real device
