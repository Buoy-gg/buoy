---
title: FloatingDevTools
seoTitle: "FloatingDevTools API — props, config & tool registration"
id: floating-devtools
description: "API reference for FloatingDevTools, the draggable floating button that opens React Buoy's in-app debugging menu in your React Native or Expo app."
---

The `FloatingDevTools` component is the entry point for React Buoy. It renders a draggable floating button that opens a menu containing all your installed debugging tools.

<!-- ::floating-menu-live-demo -->

The dial shows the most recently opened tool first. Tools you haven’t opened keep their registration order. The order updates when you reopen the dial.

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

Pass your account key and install the tools you need. Keep `FloatingDevTools` inside the providers those tools use. Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

Without a verified account, the launcher shows **Sign in**. Open it to copy `npx buoy login`, run the command in your project, then reload your app. **Check connection** retries verification and shows feedback if your account is still disconnected. The sign-in card and launcher share their design across native and web.

## Environment Badge

The floating button automatically displays your current environment based on `NODE_ENV`, helping your team instantly know where they are. No configuration needed.

You can override it explicitly if your environment name doesn't match `NODE_ENV`:

```tsx
<FloatingDevTools environment="qa" />
```

Supported values: `"local"`, `"dev"`, `"staging"`, `"qa"`, `"prod"`

## How Tools Auto-Register

When you install a Buoy tool package (like `@buoy-gg/network` or `@buoy-gg/storage`), it automatically registers itself with the floating menu. Some tools need additional registration or app configuration.

```bash
npm install @buoy-gg/network
```

Restart Metro after installing the package, then open Network to confirm discovery.

## Custom Tools

Need something specific to your app? Add your own tools:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { View, Text } from "react-native";

const FeatureFlagTool = () => (
  <View>
    <Text>Toggle feature flags here</Text>
  </View>
);

function App() {
  return (
    <FloatingDevTools
      apps={[
        {
          id: "flags",
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

`FloatingDevTools` is also the source of truth for Buoy's other surfaces. The same tools you see in the menu sync out over a local broker, after you install `@buoy-gg/external-sync` and configure the connection. You can then:

- open the [Buoy Desktop](./desktop) dashboard and inspect the same live app on a full screen, or
- point an AI agent at your app with the [MCP server](./mcp).

Just connect either one to your running app. The broker address is derived automatically from the Metro dev server that served the bundle, so physical devices reach your machine with zero config (Android over USB: run `adb reverse tcp:42831 tcp:42831` once); pass `socketURL` in the `externalSync` prop only for tunnels or a broker on another machine. The current sync target and connection state show up in the menu's Settings tab under **DESKTOP SYNC**. Each install identifies itself with a per-device id minted on first connect, so a phone and a simulator (or a whole QA team) on the same build show up as separate devices; pass `deviceName`/`deviceId` in `externalSync` only to pin a label, and never the same `deviceId` on two devices. Sync is dev-only unless you ask for it — see [release builds](./desktop#release-builds) to profile a release build you own.

## Headless (sync-only) mode

For builds that ship to non-developers — field or associate builds where the desktop dashboard is the only debugging surface — mount the tools with no on-device UI at all:

```tsx
<FloatingDevTools headless />
```

`headless` keeps every tool's sync adapter and route tracking running (so Buoy Desktop and the MCP server see the full session) but renders no floating button, dial, or overlays. Headless mode still requires a verified account. Supply the key through initialization or `licenseKey`; it has no on-device account entry screen. `requireLicense: false` does not bypass account admission. Sync still follows the same rule as any other build: on in dev, and in a release build only with [`externalSync.enableInRelease`](./desktop#release-builds) plus a Pro license — which is exactly what a field build wants.

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Buoy Desktop](./desktop) — Inspect the same app on a full dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Quick Start](./quick-start) — Full setup walkthrough

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](./web-preview.md) for registration, dependencies, and browser boundaries.

The browser dial supports Tab, arrow keys between tools, Enter to activate controls, and Escape to close. Its center button opens settings. Drag panel backgrounds or handles to move them; tabs and inputs keep their normal mouse behavior. Minimized tools stay above the floating bar and scroll when needed. They open below only when there is not enough room above for one row.

The web dial follows the shared Background selection in Settings, including changes made while the dial is open.
