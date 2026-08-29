---
title: Quick Start
seoTitle: "React Native TV DevTools Setup — install Buoy on Apple TV & Android TV"
id: tv-quick-start
description: "Get Buoy running in a React Native TV app in minutes: install the core, mount it headless, and open your Apple TV or Android TV device in Buoy Desktop."
---

Get a TV app streaming to Buoy Desktop in under 2 minutes. There is no separate TV SDK — you
install the same packages you would in any React Native app.

## 1. Install the core

<!-- ::PM npm="npm install @buoy-gg/core" yarn="yarn add @buoy-gg/core" pnpm="pnpm add @buoy-gg/core" bun="bun add @buoy-gg/core" -->

## 2. Mount it headless

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools headless />  {/* TV apps run headless — no bubble */}
    </>
  );
}
```

`headless` mounts every installed tool's sync adapter plus the route, console and store
instrumentation, and renders **no on-device UI at all** — no bubble, no dial, no overlays. It is
not a TV-specific flag; it is the same mode shipped for field builds where only the desktop should
see the session. See [Overview](./overview#why-there-is-no-floating-menu-on-tv) for why that is the
right call on TV rather than a compromise.

Buoy adds **no native dependencies** to a TV app — no podspecs, no gradle edits, no prebuild — so
this needs only a Metro restart.

## 3. Add the TV tools

<!-- ::PM npm="npm install @buoy-gg/tv-remote @buoy-gg/focus-inspector" yarn="yarn add @buoy-gg/tv-remote @buoy-gg/focus-inspector" pnpm="pnpm add @buoy-gg/tv-remote @buoy-gg/focus-inspector" bun="bun add @buoy-gg/tv-remote @buoy-gg/focus-inspector" -->

Nothing else to wire — installed tools register themselves. Add any other tool the same way
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

Then open **Focus** and drive the app: every focus move is credited to the direction that caused
it, and focus that gets stuck, vanishes, or is never reached gets flagged.

## Requirements

- `react-native-tvos` and the New Architecture (Fabric).
- Buoy adds no native dependencies — a Metro restart is the whole install step.
- Headless has **no license entry UI**. A Pro key can only arrive as the `licenseKey` prop. The
  desktop dashboard works at the free tier; the MCP server requires Pro.

More detail in [Installation](./installation).
