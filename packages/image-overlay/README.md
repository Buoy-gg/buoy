# @buoy-gg/image-overlay

[![npm version](https://img.shields.io/npm/v/@buoy-gg/image-overlay?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/image-overlay) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/image-overlay?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/image-overlay)

**Pin your Figma mockup on top of the running app and nail pixel-perfect UI without leaving the simulator.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/image-overlay
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

The IMG tool appears in the menu. Copy a frame in Figma, tap "Paste from Clipboard" (or paste an image URL), and the mockup floats over your live UI.

To pin the overlay to a specific component in **Component Mode**, tag it with an `image-target:` testID:

```tsx
<View testID="image-target:LoginCard">
  <TextInput placeholder="Email" />
  <Button title="Sign In" />
</View>
```

The text after `image-target:` becomes the label in the target list. Your existing testIDs (like `testID="login-button"`) are ignored — only the `image-target:` prefix is scanned.

## What you get

- **Two image sources** — paste straight from the clipboard (Figma copy → paste; requires `expo-clipboard`) or load any image URL (works in bare React Native too)
- **Component Mode** — scan the app for tagged targets, pick one, and the overlay pins to its exact measured position and size
- **Auto-track** — the target is re-measured every render, so the overlay stays locked through scrolling, animation, and layout shifts
- **Free Mode** — drag and pinch the overlay anywhere for full-screen or multi-component comparisons
- **Opacity blending** — fade the mockup over your live UI to spot the differences instantly
- **Fine alignment** — scale, X/Y offset, horizontal/vertical flip, and an outline toggle for precise placement
- **Lock** — freeze position so you don't nudge it mid-comparison

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/image-overlay) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
