---
title: TV Remote
seoTitle: "React Native TV Remote Driver — press the D-pad from your desktop"
id: tools-tv-remote
description: "Drive an Apple TV or Android TV app's D-pad from Buoy Desktop, record press sequences as macros, and replay them across every connected device."
---

<!-- ::platform-badge platform="both" -->

Press the remote without picking one up. The TV Remote tool sends real D-pad, select, menu, media
and long presses — and typed text — to a connected TV app from Buoy Desktop, records what you
pressed as a named macro, and replays that macro against one device, or every mapped device at
once.

It exists because TV navigation regressions are the ones nobody catches: a card that used to be
reachable with three RIGHTs now needs four, a modal traps focus, a row silently swallows UP. A
recorded macro replays that exact path and tells you, step by step, whether the app actually
received each press.

## Installation

<!-- ::PM npm="npm install @buoy-gg/tv-remote" yarn="yarn add @buoy-gg/tv-remote" pnpm="pnpm add @buoy-gg/tv-remote" bun="bun add @buoy-gg/tv-remote" -->

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

Then open **TV Remote** in [Buoy Desktop](../desktop), pair your device with an injection target,
and press away.

---

## How presses are delivered — and why it matters

**Buoy injects presses from your Mac, not from inside your app.** Buoy Desktop shells out to
`adb shell input keyevent` for Android TV and `idb ui key` for the Apple TV simulator. Both go
through the platform's real input pipeline, so the press travels the same focus engine a physical
remote drives.

The package you install in the app does the other half: it **observes**. It reports the remote
events your app actually received, which is what lets a replay tell "the app handled that press"
apart from "something swallowed it."

Nothing is faked in-process, and that is deliberate:

- On tvOS, an app can post React Native's internal key-event notification (the JS event fires, but
  focus never moves) or call `requestTVFocus` (focus jumps, but no event fires and nothing is
  traversed). Each is half of a press, and the missing half is the half QA cares about.
- On Android, directional focus navigation for unconsumed D-pad keys happens in `ViewRootImpl`,
  *above* the Activity. An in-process `dispatchKeyEvent` would fire JS events **without moving
  focus** exactly on the screens that are broken.

The package also renders nothing on screen. On Android TV any focusable view in an overlay becomes
a D-pad stop in your app's focus order — a visible tool would change the navigation it is meant to
be testing.

---

## What works where

| Target | Replay | How |
|---|---|---|
| Android TV emulator | ✅ everything | `adb -s <serial> shell input keyevent` |
| Android TV device | ✅ everything | the same, over `adb connect <ip>:5555` |
| Apple TV simulator | ✅ D-pad, select, menu, hold, Home, text — ❌ media keys | `idb ui key` / `ui button` / `ui text` |
| Apple TV device | ❌ **record only** | no supported host-side injection exists |

### What you can send

| | Apple TV sim | Android TV | Echoed back by the app? |
|---|---|---|---|
| D-pad, Select, Menu | ✅ | ✅ | yes (Menu: only with menu capture on) |
| **Hold** (long press) | ✅ | ✅ | yes — as `longSelect`, `longLeft`, … |
| **Type text** | ✅ | ✅ | **no** — see below |
| Play/pause, rewind, fast-forward, next, previous | ❌ | ✅ | yes, on Android |
| Home / TV | ✅ | ✅ | no — it backgrounds the app |
| Swipe / pan (touch surface) | ❌ | ❌ | — |
| Voice / Siri | ❌ | ❌ | — |

Some limits are worth stating plainly rather than discovering later:

- **No media transport keys on an Apple TV simulator.** `idb ui key` speaks the HID *keyboard*
  page, which has no usages for play/pause, rewind, fast-forward, next or previous, and
  `idb ui button` offers only Apple Pay, Home, Lock, Side button and Siri. Those steps are reported
  `skipped-unsupported` on that target — flagged, never silently passed. They work on Android.
- **A retail Apple TV cannot be driven at all.** Apple's only supported way to press buttons on
  physical hardware programmatically is an XCUITest runner paired to the device, which is far too
  heavy to live inside Buoy Desktop.
- **Swipes and pans can be recorded but never replayed.** There is no touch surface on a simulated
  Apple TV (`idb ui swipe` errors out and takes the app with it), and Android TV has no
  touchscreen. So `swipeUp`/`pan` events reach the capture stream from a physical remote, but no
  macro step can reproduce them.
- **Voice / Siri is out of reach, and would not help.** Siri is a system service: the audio goes to
  Apple and your app never sees it, so there is nothing to inject and nothing to assert. What your
  app actually observes is the resulting *text* — which is what `Type text` sends.

**Recording works everywhere, including retail hardware**, because capture is pure JS. That gives
you the *record-on-retail* workflow: a tester presses the physical remote on a rack device, the
macro is recorded from the app's own event stream, and replay then runs against emulators and
simulators.

### Requirements on your Mac

- **Android**: `adb` — part of the Android SDK platform-tools.
- **Apple TV simulator**: `idb`, which does **not** ship with Xcode:

  ```bash
  brew tap facebook/fb && brew install idb-companion
  pipx install fb-idb
  ```

  Without it the Apple TV lane is disabled and the panel says so.

Buoy looks for both on your `PATH` and in the usual install locations, so a packaged desktop app
finds them even though a GUI app inherits a minimal `PATH`.

---

## Pairing

The panel lists every booted target it finds and suggests one that matches your device's platform.
Pairing is **manual and remembered** per device. Buoy will not probe for a pairing by injecting keys
into devices you didn't ask it to touch.

---

## Typing, and holding

**Type text** sends a string to whatever field currently has focus
(`idb ui text` / `adb shell input text`). This is the practical stand-in for the Siri remote's
dictation, and it saves pecking out a search query on a D-pad keyboard grid one letter at a time.

One caveat, and it is a real one: text travels the platform's **keyboard** path, so it reaches the
native text field without ever touching the app's TV event pipe. Verified: an app with capture
armed reports nothing at all for injected text. A text step is therefore **fire-and-wait** — the
tool can prove it was sent, not that it was received. (Menu and Home are fire-and-wait for the same
kind of reason.)

**Hold** turns the next press into a long press — the same keycode with a duration flag
(`--longpress` / `--duration`). React Native reports it under its own event name, so a held Select
comes back as `longSelect` and *is* echo-confirmed like any other press. Holding is meaningful for
the D-pad, Select and Play/Pause; the fork emits no long variant for Menu.

## Macros

A macro is a list of raw presses with the timing you recorded:

```json
{
  "version": 1,
  "name": "search-for-arrow",
  "createdAt": "2026-08-25T14:02:11.000Z",
  "steps": [
    { "key": "right", "delayMs": 420 },
    { "key": "select", "delayMs": 800 },
    { "key": "select", "hold": true, "delayMs": 600 },
    { "kind": "text", "text": "arrow", "delayMs": 500 }
  ]
}
```

A step is either a key press (`key`, optionally `hold`) or typed text (`kind: "text"`). A step with
no `kind` has always meant a key press, so macros recorded before text steps existed still replay.

Record one two ways:

- **From the D-pad** — every button you press in the panel is injected *and* recorded. No capture
  gaps, because the desktop knows exactly what it sent.
- **From the real remote** — a human presses the physical remote and the macro is built from the
  app's capture stream. This is the retail-device path.

Macros export and import as JSON, so they can live in the repo next to the app under test.

### Replay

Each step is injected, then Buoy waits for the app to echo the event back before pacing the recorded
delay and moving on. Recorded delays are clamped to 150ms–5s and scaled by the speed multiplier, so
a long pause while you were thinking doesn't become a long pause in CI.

Every step reports one of:

| Outcome | Means |
|---|---|
| **ok** | Injected, and the app reported receiving it (with the round-trip time). |
| **no-echo** | Injected, but the app never saw it. Something above it swallowed the key — a native video player holding focus, a modal, or a wedged JS thread. This is a finding, not noise. |
| **skipped-unsupported** | This target has no way to send that key (play/pause on a tvOS simulator). |
| **inject-failed** | The injector itself failed — target offline, adb unauthorized, idb missing. |

Menu is expected to produce no echo: on Android the back key goes to `BackHandler` rather than the
TV event pipe, and on tvOS the Menu key is handled natively. Turn on **Capture the Menu key** while
recording on Apple TV to route it to JS instead — the panel warns you that your app won't go back or
background while that is on, and it is always handed back when recording stops.

### Fleet replay

**Run on N devices** replays one macro against every paired device at once, one injection lane each,
and lays the outcomes out as a device × step matrix with a final screenshot per device for eyeball
diffing. Partial fleets are normal: an Apple TV simulator skipping a play/pause step, or one device
being offline, marks that cell and never blocks the rest.

---

## What's Next

- [Buoy Desktop](../desktop) — where the TV Remote panel lives
- [Route Inspector](./routes) — see which screen a replay landed on
- [Highlight Updates](./highlight-updates) — what re-rendered while focus moved

---

## FAQ

### Can I test my app on a real Apple TV with this?

You can **record** on one — capture runs in your app and works on any hardware. You cannot replay
against one in this version; there is no supported host-side way to inject remote presses into a
retail Apple TV.

### Why doesn't the tool show anything on the TV screen?

On purpose. On Android TV a focusable view in an overlay joins your app's D-pad focus order, so an
on-screen tool would alter the navigation it is supposed to be testing.

### My macro reports `no-echo` on a video screen.

That is the tool doing its job. Native players (ExoPlayer, AVPlayer) take focus and eat D-pad and
play/pause input while video is mounted. A fixed-delay replay would have marched on and reported a
pass.

### Can I use the Siri button / voice search?

No. `idb` does expose a `SIRI` button, but the simulator has no Siri, and on real hardware Siri is a
system service — the audio goes to Apple and your app never receives it. There is nothing to inject
and nothing for the app to echo. Use **Type text** instead: the text is the part your app actually
sees.

### Does this need a native module?

No. `@buoy-gg/tv-remote` is pure JavaScript, like every Buoy package. The shelling out happens in
the desktop app.
