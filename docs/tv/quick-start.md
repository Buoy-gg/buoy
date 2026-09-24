---
title: Quick Start
seoTitle: "React Native TV DevTools Setup — install Buoy on Apple TV & Android TV"
id: tv-quick-start
description: "Get Buoy running in a React Native TV app in minutes: install the core, mount it headless, and open your Apple TV or Android TV device in Buoy Desktop."
---

Connect a React Native TV app to Buoy Desktop, then inspect remote input and focus. Buoy uses the same JavaScript packages as its phone integration.

## 1. Install the core

<!-- ::PM npm="npm install @buoy-gg/core @buoy-gg/external-sync" yarn="yarn add @buoy-gg/core @buoy-gg/external-sync" pnpm="pnpm add @buoy-gg/core @buoy-gg/external-sync" bun="bun add @buoy-gg/core @buoy-gg/external-sync" -->

For plain Markdown readers, the command is:

```bash
npm install @buoy-gg/core @buoy-gg/external-sync
```

Configure a Free or Pro key with `npx --package=@buoy-gg/core buoy login`. The examples use Expo's `EXPO_PUBLIC_BUOY_KEY`; React Native CLI apps must supply the key through their own environment configuration.


## 2. Mount it headless

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools headless licenseKey={process.env.EXPO_PUBLIC_BUOY_KEY} />  {/* TV apps run headless — no bubble */}
    </>
  );
}
```

`headless` mounts every installed tool's sync adapter plus the route, console and store
instrumentation, and renders **no on-device UI at all** — no bubble, no dial, no overlays. It is
not a TV-specific flag; it is the same mode shipped for field builds where only the desktop should
see the session. See [Overview](./overview#why-there-is-no-floating-menu-on-tv) for why that is the
right call on TV rather than a compromise.

Buoy adds **no native dependencies** to a TV app. Restart Metro after adding the packages. Follow the requirements of any other dependencies you add.

## 3. Add the TV tools

<!-- ::PM npm="npm install @buoy-gg/tv-remote @buoy-gg/focus-inspector" yarn="yarn add @buoy-gg/tv-remote @buoy-gg/focus-inspector" pnpm="pnpm add @buoy-gg/tv-remote @buoy-gg/focus-inspector" bun="bun add @buoy-gg/tv-remote @buoy-gg/focus-inspector" -->

The TV tools register when installed. Other tools may need app-specific configuration. Add any other tool the same way
(`@buoy-gg/network`, `@buoy-gg/storage`, …) and it appears in the desktop dashboard.

## 4. Connect Buoy Desktop

Open [Buoy Desktop](../desktop) and your TV appears in the device switcher next to your phones.

An **Android TV emulator** needs the broker port forwarded once per device:

```sh
adb -s <serial> reverse tcp:42831 tcp:42831
```

Pass `-s <serial>` explicitly — a physical phone is often attached at the same time, and `adb`
will otherwise pick the wrong one.

An **Apple TV simulator** connects on its own.

## 5. Press the remote from your desktop

Open **TV Remote** in Buoy Desktop, pair the device with an injection target, and press. Presses go
through the platform's real input pipeline, so they move focus exactly like a physical remote.

Injection needs `adb` on your `PATH` (Android SDK platform-tools) and, for the Apple TV simulator,
`idb` — which does not ship with Xcode:

```sh
brew tap facebook/fb && brew install idb-companion
pipx install fb-idb
```

Without it the Apple TV lane is disabled and the panel says so. A retail Apple TV can be **recorded
but not driven** — see the target table in the [Overview](./overview#what-works-where).

Then open **Focus** and drive the app: reported focus moves can be compared with observed direction inputs. Review flags against actual device behavior.

## Requirements

- `react-native-tvos` and the New Architecture (Fabric).
- Restart Metro after adding JavaScript tools. Your TV runtime and host input tools have separate setup requirements.
- Headless has **no license entry UI**. Configure your Free or Pro account key before mounting the headless tools. The
  desktop dashboard works at the free tier; the MCP server requires Pro.

More detail in [Installation](./installation).
