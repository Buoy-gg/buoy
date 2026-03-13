---
title: FloatingDevTools
id: floating-devtools
---

The `FloatingDevTools` component is the entry point for React Buoy. It renders a draggable floating button that opens a menu containing all your installed debugging tools.

## Basic Usage

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools environment="qa" />
    </>
  );
}
```

That's it. Any Buoy tool packages you've installed will automatically appear in the menu.

## Register Your License Key

A license key is required for all plans. Call `Buoy.init()` before rendering `FloatingDevTools`:

```tsx
import { Buoy, FloatingDevTools } from "@buoy-gg/core";

// Register your license key
Buoy.init({
  licenseKey: "YOUR_LICENSE_KEY",
});

function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools environment="qa" />
    </>
  );
}
```

You can also use the async variant if you need to wait for validation to complete:

```tsx
await Buoy.initAsync({
  licenseKey: "YOUR_LICENSE_KEY",
});
```

Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

## Environment Badge

The floating button displays your current environment, helping your team instantly know where they are:

```tsx
<FloatingDevTools environment="qa" />
```

Common values: `"local"`, `"dev"`, `"staging"`, `"qa"`, `"production"`

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
      environment="qa"
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

## Default-Enabled Tools

By default, all tools (both built-in and custom) are disabled in the dial and floating row
until the user enables them via the settings modal. To have a tool appear immediately without
requiring setup, set `enabledByDefault: true` on the `InstalledApp` config:

```tsx
<FloatingDevTools
  environment="qa"
  customTools={[
    {
      name: "Flags",
      component: FeatureFlagTool,
      icon: "🚩",
      enabledByDefault: true, // Visible in dial on first launch
    },
  ]}
/>
```

User preferences (persisted via AsyncStorage) always take precedence. Once a user
explicitly toggles a tool in the settings modal, `enabledByDefault` is no longer consulted.

Built-in tool packages can also opt into this by exporting their preset with `enabledByDefault: true`.

## Draggable Button

The floating button can be dragged anywhere on screen. It remembers its position between sessions, so it stays where your team likes it.

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Quick Start](./quick-start) — Full setup walkthrough
