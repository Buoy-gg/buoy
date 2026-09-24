# @buoy-gg/image-overlay

[![npm version](https://img.shields.io/npm/v/@buoy-gg/image-overlay?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/image-overlay) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/image-overlay?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/image-overlay)

Place a reference image over your app to compare spacing, alignment, and sizing on the device.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/image-overlay
```

## Before you start

Use a development build with `@buoy-gg/core` and a Free or Pro Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

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

To pin the overlay to a specific component in **Component Mode**, tag it with an `image-target:` testID. This example uses your existing `handleSignIn` handler and React Native components:

```tsx
<View testID="image-target:LoginCard">
  <TextInput placeholder="Email" />
  <Button title="Sign In" onPress={handleSignIn} />
</View>
```

The text after `image-target:` becomes the label in the target list. Your existing testIDs (like `testID="login-button"`) are ignored — only the `image-target:` prefix is scanned.

## Check the integration

Load a reference image from a direct image URL, adjust its opacity, and align it with a known screen. Clipboard import requires the clipboard dependency described in the full setup guide.

## What you get

- **Two image sources** — paste straight from the clipboard (Figma copy → paste; requires `expo-clipboard`) or load any image URL (works in bare React Native too)
- **Component Mode** — scan the app for tagged targets, pick one, and the overlay pins to its exact measured position and size
- **Auto-track** — re-measures the target as the app changes. Check alignment during scrolling and animation on your target device
- **Free Mode** — drag and pinch the overlay anywhere for full-screen or multi-component comparisons
- **Opacity blending** — fade the mockup over your live UI to spot the differences instantly
- **Fine alignment** — scale, X/Y offset, horizontal/vertical flip, and an outline toggle for precise placement
- **Lock** — freeze position so you don't nudge it mid-comparison

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/image-overlay) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Tag browser targets with data-testid="image-target:Name" and import the browser registration before React DOM. The shared controls support target and free placement. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
