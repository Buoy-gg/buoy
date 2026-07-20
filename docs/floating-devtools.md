---
title: FloatingDevTools
id: floating-devtools
description: "API reference for FloatingDevTools, the draggable floating button that opens React Buoy's in-app debugging menu in your React Native or Expo app."
---

The `FloatingDevTools` component is the entry point for React Buoy. It renders a draggable floating button that opens a menu containing all your installed debugging tools.

## Basic Usage

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
    </>
  );
}
```

That's it. Pass your license key as a prop and any Buoy tool packages you've installed will automatically appear in the menu. Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

## Environment Badge

The floating button automatically displays your current environment based on `NODE_ENV`, helping your team instantly know where they are. No configuration needed.

You can override it explicitly if your environment name doesn't match `NODE_ENV`:

```tsx
<FloatingDevTools environment="qa" />
```

Supported values: `"local"`, `"dev"`, `"staging"`, `"qa"`, `"prod"`

## How Tools Auto-Register

When you install a Buoy tool package (like `@buoy-gg/network` or `@buoy-gg/storage`), it automatically registers itself with the floating menu. No imports, no configuration, no wiring.

```bash
npm install @buoy-gg/network
```

The Network tool now appears in your menu. That's the magic of Buoy.

## Custom Tools

Need something specific to your app? Add your own tools:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

const FeatureFlagTool = () => (
  <View>
    <Text>Toggle feature flags here</Text>
  </View>
);

function App() {
  return (
    <FloatingDevTools
      customTools={[
        {
          name: "Flags",
          component: FeatureFlagTool,
          icon: "🚩",
        },
      ]}
    />
  );
}
```

See [Custom Tools](./custom-tools) for more details on building your own debugging tools.

## Draggable Button

The floating button can be dragged anywhere on screen. It remembers its position between sessions, so it stays where your team likes it.

## Beyond the in-app menu

`FloatingDevTools` is also the source of truth for Buoy's other surfaces. The same tools you see in the menu sync out over a local broker, so once this is set up you can — with no extra code — also:

- open the [Buoy Desktop](./desktop) dashboard and inspect the same live app on a full screen, or
- point an AI agent at your app with the [MCP server](./mcp).

Just connect either one to your running app. The broker address is derived automatically from the Metro dev server that served the bundle, so physical devices reach your machine with zero config (Android over USB: run `adb reverse tcp:42831 tcp:42831` once); pass `socketURL` in the `externalSync` prop only for tunnels or a broker on another machine. The current sync target and connection state show up in the menu's Settings tab under **DESKTOP SYNC**.

## Headless (sync-only) mode

For builds that ship to non-developers — field or associate builds where the desktop dashboard is the only debugging surface — mount the tools with no on-device UI at all:

```tsx
<FloatingDevTools headless />
```

`headless` keeps every tool's sync adapter and route tracking running (so Buoy Desktop and the MCP server see the full session) but renders no floating button, dial, or overlays. Desktop sync stays dev-build-only; `requireLicense` is ignored in headless mode since there is no UI to gate.

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Buoy Desktop](./desktop) — Inspect the same app on a full dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Quick Start](./quick-start) — Full setup walkthrough
