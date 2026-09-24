---
title: Overview
seoTitle: "Buoy for React Native TV — devtools for Apple TV & Android TV apps"
id: tv-overview
description: "Meet Buoy on React Native TV (beta) — mount headless, debug Apple TV and Android TV apps from Buoy Desktop, drive the D-pad, and find focus bugs."
---

TV is React Native — the same packages, the same install. What changes is the **surface**. A TV has
no touch, so Buoy mounts **headless** and renders nothing on the screen, and
[Buoy Desktop](../desktop) becomes the whole interface.

TV support is in beta. The documented target set includes Apple TV simulators and Android TV emulators. Check [Known limits](#known-limits) and test your app on its actual target hardware; these pages are not a test record for every device and build.

**Two ways to reach your running TV app:**

- **On your desktop** — Buoy Desktop, a free dashboard for macOS, Windows & Linux. Your TV device
  appears in the same switcher as your phones, with the same panels.
- **Through your AI** — the [MCP server](../mcp) lets Claude Code, Cursor, or any MCP editor
  inspect your live TV app (Pro).

There is no third way on TV: the in-app floating menu is deliberately absent.

## Why there is no floating menu on TV

TV uses D-pad navigation. A focusable debug overlay can enter that focus order and affect the behavior under test. Mount Buoy headless to keep its controls on Desktop and leave the app screen available for inspection.

## The two TV tools

These exist *because* of TV. Both are desktop surfaces — the packages you install only capture.

| Tool | Package | What it does |
|---|---|---|
| [TV Remote](../tools/tv-remote) | `@buoy-gg/tv-remote` | Press the D-pad, Select, Menu, holds, media keys and typed text from Buoy Desktop; record a macro and replay it with per-step confirmation. |
| [Focus Inspector](../tools/focus-inspector) | `@buoy-gg/focus-inspector` | Every focus move with its direction, the full focusable inventory, and flags for focus that gets **stuck**, **vanishes**, or is **never reached**. |

Use the remote event history and focus transitions together to record the sequence leading to a focus problem.

### Presses come from your Mac, not from inside the app

Buoy Desktop injects with `adb shell input keyevent` on Android TV and `idb ui key` on the Apple TV
simulator. Both travel the platform's real input pipeline, so focus moves through the same engine a
physical remote drives. The installed package only **observes** — it reports which events your app
actually received, which is what tells "the app handled that press" apart from "something swallowed
it."

The app package observes input events; host tools inject supported presses. A JavaScript handler call alone does not reproduce the platform focus-navigation path.

### What works where

| Target | Capture | Replay presses | How |
|---|---|---|---|
| Android TV emulator | ✅ | ✅ everything | `adb -s <serial> shell input keyevent` |
| Android TV device | ✅ | ✅ everything | the same, over `adb connect <ip>:5555` |
| Apple TV simulator | ✅ | ✅ except media keys | `idb ui key` / `ui button` / `ui text` |
| Apple TV device | ✅ | ❌ **record only** | no supported host-side injection exists |

Capture is pure JavaScript, so **recording works everywhere, including retail hardware.** That is
the record-on-retail workflow: a tester presses the physical remote on a rack device, the macro is
built from the app's own event stream, and replay runs against emulators and simulators.

## The rest of the suite

None of these needed TV-specific code — they are the same tools the phone examples use, available for inspection through the corresponding installed packages:

[Network](../tools/network) · [Storage](../tools/storage) · [Console](../tools/console) ·
[Env](../tools/env) · [Routes](../tools/routes) · [Events](../tools/events) ·
[React Query](../tools/react-query) · [Redux](../tools/redux) · [Zustand](../tools/zustand) ·
[Jotai](../tools/jotai)

## Known limits

- **No on-device UI, at all.** Anything that draws on the app to do its job — render highlighting,
  debug borders, the image overlay — has nothing to draw into on TV.
- **A retail Apple TV can be recorded, not driven.** Apple's only supported path for pressing
  buttons on physical hardware is an XCUITest runner paired to the device.
- **No media transport keys on a tvOS simulator.** `idb ui key` speaks the HID *keyboard* page,
  which has no usages for play/pause, rewind, fast-forward, next or previous. Those steps report
  `skipped-unsupported`, never silently pass. They work on Android.
- **Typed text never echoes.** It rides the platform's keyboard path and reaches the native text
  field without touching the app's TV event pipe, so a text step is fire-and-wait.
- **Swipes and pans replay nowhere.** There is no touch surface on either platform, so a swipe
  recorded from a physical Siri remote has no step that can reproduce it.
- **Tools beyond the list above are untested on TV.** Not blocked — just not device-verified yet.

## Coming soon

- MCP tools for the remote, so an agent can press the D-pad and replay macros.
- An XCUITest lane for retail Apple TV — the one target replay cannot reach today.
- Macros in CI, with each step's echo as the assertion.

Next: [Quick Start](./quick-start).
