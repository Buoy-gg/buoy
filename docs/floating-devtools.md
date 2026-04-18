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

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Quick Start](./quick-start) — Full setup walkthrough
