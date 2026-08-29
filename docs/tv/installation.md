---
title: Installation
seoTitle: "Install React Native TV DevTools — Buoy setup for Apple TV & Android TV"
id: tv-installation
description: "Full install guide for Buoy on React Native TV: requirements, packages, headless mount, host tooling (adb and idb), device connection, and license keys."
---

Buoy on TV is the ordinary React Native install with one prop changed. This page is the complete
version of [Quick Start](./quick-start).

## Requirements

| | |
|---|---|
| React Native | `react-native-tvos` (the TV fork) |
| Architecture | New Architecture / Fabric |
| App type | Expo TV (`@react-native-tvos/config-tv`) or bare RN TV — both verified |
| Native deps added by Buoy | **None** — no podspecs, no gradle changes, no prebuild |
| Host tooling | `adb` for Android TV; `idb` for the Apple TV simulator (remote injection only) |

Expo Go cannot run a TV app at all — TV is always a prebuild + `expo run:*` app. That is a property
of TV, not of Buoy.

## Packages

Install the core, then any tools you want. Installed tools register themselves; there is no list to
maintain.

<!-- ::PM npm="npm install @buoy-gg/core" yarn="yarn add @buoy-gg/core" pnpm="pnpm add @buoy-gg/core" bun="bun add @buoy-gg/core" -->

The two TV-specific tools:

<!-- ::PM npm="npm install @buoy-gg/tv-remote @buoy-gg/focus-inspector" yarn="yarn add @buoy-gg/tv-remote @buoy-gg/focus-inspector" pnpm="pnpm add @buoy-gg/tv-remote @buoy-gg/focus-inspector" bun="bun add @buoy-gg/tv-remote @buoy-gg/focus-inspector" -->

Everything else installs exactly as it does on a phone — [Network](../tools/network),
[Storage](../tools/storage), [Console](../tools/console), [Env](../tools/env),
[Routes](../tools/routes), [Events](../tools/events), [React Query](../tools/react-query),
[Redux](../tools/redux), [Zustand](../tools/zustand), [Jotai](../tools/jotai) are all
device-verified on both TV platforms.

## Mounting

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools
        headless
        licenseKey={process.env.EXPO_PUBLIC_BUOY_KEY}
        externalSync={{ deviceId: DEVICE_ID, deviceName: "Living Room Apple TV" }}
      />
    </>
  );
}
```

`headless` is required on TV in practice: without it the floating bubble renders, and on Android TV
its focusable views join your app's D-pad focus order. See
[Overview](./overview#why-there-is-no-floating-menu-on-tv).

`deviceName` is what shows in Buoy Desktop's switcher. Give each TV a distinct one — an Apple TV
simulator and an Android TV emulator running the same app are otherwise hard to tell apart.

## Connecting to the desktop

| Device | What it needs |
|---|---|
| Apple TV simulator | Nothing — it connects on its own. |
| Android TV emulator | `adb -s <serial> reverse tcp:42831 tcp:42831`, once per boot. |
| Physical TV device | A `socketUrl` pointing at your machine's LAN address. |

Pass `-s <serial>` to `adb` explicitly. A physical phone is often attached alongside the emulator,
and an unqualified `adb reverse` forwards the wrong device.

## Host tooling for the TV Remote

Replay is injected from your machine, so the binaries have to be findable:

- **Android** — `adb`, part of the Android SDK platform-tools.
- **Apple TV simulator** — `idb`, which does **not** ship with Xcode:

  ```sh
  brew tap facebook/fb && brew install idb-companion
  pipx install fb-idb
  ```

Buoy looks for both on your `PATH` and in the usual install locations, so the packaged desktop app
finds them even though a GUI app inherits a minimal `PATH`. If `idb` is missing, the Apple TV lane
is disabled and the panel says so rather than failing silently.

Recording needs none of this — capture is pure JavaScript and works on retail hardware.

## License keys

| | Key | What you get |
| --- | --- | --- |
| **No key** | none | Every tool works, with a minimal event history. |
| **Free** | free key, from an account | Standard access — event history across every tool. |
| **Pro** | paid key | Everything: production builds, the MCP server, and unlimited events. |

**Headless has no license entry UI** — there is no on-device screen to type into. On TV a key can
only arrive as the `licenseKey` prop, typically from an env var. The desktop dashboard works at the
free tier; the [MCP server](../mcp) requires Pro and will refuse an anonymous-tier TV device.

Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).
