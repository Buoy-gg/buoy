---
title: Overview
seoTitle: "Buoy for React Native TV — devtools for Apple TV & Android TV apps"
id: tv-overview
description: "Meet Buoy on React Native TV (beta) — mount headless, debug Apple TV and Android TV apps from Buoy Desktop, drive the D-pad, and find focus bugs."
---

TV is React Native — the same packages, the same install. What changes is the **surface**. A TV has
no touch, so Buoy mounts **headless** and renders nothing on the screen, and
[Buoy Desktop](../desktop) becomes the whole interface.

This is a **beta**. Everything in these pages is device-verified on an Apple TV 4K simulator and an
Android TV emulator, from both an Expo TV app and a bare React Native TV app. Anything not listed
here has not been verified on TV yet — see [Known limits](#known-limits).

**Two ways to reach your running TV app:**

- **On your desktop** — Buoy Desktop, a free dashboard for macOS, Windows & Linux. Your TV device
  appears in the same switcher as your phones, with the same panels.
- **Through your AI** — the [MCP server](../mcp) lets Claude Code, Cursor, or any MCP editor
  inspect your live TV app (Pro).

There is no third way on TV: the in-app floating menu is deliberately absent.

## Why there is no floating menu on TV

A bubble you cannot touch is useless — but rendering nothing is also the *correct* call, not merely
the easy one.

On Android TV, **any focusable view inside an overlay becomes a D-pad stop in the host app's focus
order.** `pointerEvents="box-none"` gates *touch* and does nothing for the focus engine, so an
on-device Buoy overlay would inject invisible focus stops into the very navigation it is supposed
to be testing. A debugging tool that changes what it measures is worse than no tool.

Buoy's inertness on TV was measured, not assumed: an identical press sequence produces an identical
focus order with the tools attached and with them removed.

## The two TV tools

These exist *because* of TV. Both are desktop surfaces — the packages you install only capture.

| Tool | Package | What it does |
|---|---|---|
| [TV Remote](../tools/tv-remote) | `@buoy-gg/tv-remote` | Press the D-pad, Select, Menu, holds, media keys and typed text from Buoy Desktop; record a macro and replay it with per-step confirmation. |
| [Focus Inspector](../tools/focus-inspector) | `@buoy-gg/focus-inspector` | Every focus move with its direction, the full focusable inventory, and flags for focus that gets **stuck**, **vanishes**, or is **never reached**. |

TV bugs are the ones nobody can describe. "It gets stuck down there." "The highlight just
disappears." "You can see that button, but you can't get to it." Both tools turn that into a record.

### Presses come from your Mac, not from inside the app

Buoy Desktop injects with `adb shell input keyevent` on Android TV and `idb ui key` on the Apple TV
simulator. Both travel the platform's real input pipeline, so focus moves through the same engine a
physical remote drives. The installed package only **observes** — it reports which events your app
actually received, which is what tells "the app handled that press" apart from "something swallowed
it."

Nothing is synthesized in-process, deliberately. On tvOS an app can fire the JS key event without
moving focus, or move focus without firing the event — each is half a press, and the missing half
is the half QA cares about. On Android, directional focus for unconsumed D-pad keys is resolved
above the Activity, so an in-process dispatch would fire JS events **without moving focus** on
exactly the screens that are broken.

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

None of these needed TV-specific code — they are the same tools the phone examples use, verified on
both TV platforms:

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
