---
title: Time Machine
seoTitle: "React Native State Snapshots — save & restore app state"
id: tools-time-machine
description: "Snapshot your app's complete client-side state — storage, Redux, Zustand, Jotai, React Query — as named restore points, and jump back in one tap. Test multi-step flows without rebuilding state every iteration."
---

<!-- ::platform-badge platform="both" -->

Save a named snapshot of registered client-state sources and restore it when repeating a test. Sources can include storage, Redux, Zustand, Jotai, React Query, and the current Expo Router URL.

Check the Sources strip before capture. A snapshot covers the sources available to Buoy; it does not include backend state, component-local state, or in-flight requests.

<!-- ::time-machine-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/time-machine" yarn="yarn add @buoy-gg/time-machine" pnpm="pnpm add @buoy-gg/time-machine" bun="bun add @buoy-gg/time-machine" -->

That's it — auto-discovery finds the installed package and the TIME MACHINE tool appears in your floating menu:

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

Each state source is captured through the Buoy tool that already watches it — install the ones you use (`@buoy-gg/storage`, `@buoy-gg/redux`, `@buoy-gg/zustand`, `@buoy-gg/jotai`, `@buoy-gg/react-query`) and they register as snapshot sources automatically. The tool's Sources strip shows you exactly which sources can capture and restore, and why when one can't — including Route, which lights up on any Expo Router app and needs no extra package.

---

## What You Can Do

- **Capture a restore point** — name it, or don't: an unnamed capture is called after the screen you were on and the time (`/checkout · 14:32`), and you can rename it from the row later. Captured client state persists on-device and survives app restarts and JS reloads.
- **Switch a source off** — every tile in the Sources strip is a switch. Tap Zustand off and no capture or restore touches your Zustand stores, from the tool, the restore bar, Buoy Desktop or an agent alike, while storage, Redux, Jotai, React Query and the route keep working. Use it for a store the app cannot survive having replaced wholesale. The choice persists across reloads.
- **Keep the list yours** — swipe any restore point to duplicate or delete it, and a delete can be undone from the bar that replaces it. Renaming lives on the preview, next to what the point actually contains. The row tells you which point you last restored and when.
- **Restore in one tap** — two modes:
  - **Live restore** swaps state in place: stores are replaced, the query cache is diffed query-by-query (mounted components keep their subscriptions), storage is written back. The app stays exactly where it is.
  - **Restore + reload** restores persisted storage, then reloads the JS bundle — your in-memory stores rebuild themselves from the restored storage. Use this when your stores initialize from persisted storage.
- **Save a repeatable starting state** — configure the supported state sources, capture a snapshot and inspect its restore preview. Restore can remove supported keys absent from the snapshot. An empty baseline clears registered providers that support clearing and requests a reload; it does not reset backend state or reproduce a full reinstall.
- **Go back to the screen, not just the state** — every restore point also records the route it was captured on (`/checkout/payment`, query string and all), and "Return to this screen" sits with the other changes in the restore preview. It's on by default and it's a property of the restore point, so you answer once rather than on every restore. The app navigates as soon as the state lands — or right after the reload, when you restored in reload mode. Needs Expo Router; without it snapshots carry no route and the checkbox doesn't appear.
- **Preview before you restore** — tap any snapshot to see exactly what restoring it will change, item by item: what gets added, removed, overwritten — and what *can't* be applied, with the reason. Tap any item and its before/after diff expands inline underneath it — side by side, straight from the storage tool's diff UX, without losing your place in the list.
- **Untick what you don't want back — and it sticks** — every change in the preview has a checkbox. Uncheck the ones a restore should leave alone (that one auth token, a device id, a cache timestamp) and the restore point remembers it immediately, no save step: every later restore skips exactly those items, including the one-tap RESTORE on the list and restores driven from MCP. Everything else still comes back, including keys that only start differing later. Ticking a source's own checkbox toggles everything under it at once.
- **Loop from a bar over the app** — when a flow needs the same restore ten times in a row, tap **Action bar** in the tool's header (or swipe a restore point and choose **Bar**). The tool minimizes and a small strip stays over your app with one restore point armed: one tap restores it, a long press restores the state without changing screens, and the status line says where a tap will take you. Every restore from the bar first saves a "Before last restore" copy, so an **Undo** link sits in the status line for ten seconds after each one. The picker (chevron) has a **Reload the app after restoring** switch: turn it on when a screen keeps its state in a React context or `useState` that reads storage only when it mounts, such as a cart provider hydrated from AsyncStorage. A live restore puts the storage key back but that screen never re-reads it; a restore that reloads makes it rebuild from the restored storage. `+` asks for a name (leave it empty for the screen and time), captures a new point right there and arms it; the chevron beside the name switches to another point. The strip comes back after a reload, drags anywhere, and hides at the screen edge from its grip. Close it with the × when the loop is done; nothing else changes.
- **See exactly what happened** — every restore reports per-source results: applied counts, skipped items with reasons, and warnings. A partial restore is reported, never hidden.
- **Drive it remotely** — the same snapshots work from Buoy Desktop and from AI agents via MCP (`time_machine_action`): *capture → drive the flow → restore → repeat* is one tool call per step.

---

## How Restore Works (per source)

| Source | Restore mechanism |
|---|---|
| **AsyncStorage** | Diff: keys missing from the snapshot are removed, snapshot entries bulk-written. Buoy's own keys are never touched. |
| **MMKV** | Per registered instance, per-key diff. Read-only instances and `ArrayBuffer` values are skipped (and reported). |
| **SecureStore** | Registered keys written back with their original options. Biometric-protected keys are never read or written. |
| **Redux** | A single full-state jump through Buoy's reducer wrapper, verified after dispatch. Works with the zero-config enhancer, or wrap your root reducer in `withBuoyDevTools()` when using the middleware. |
| **Zustand** | Full `setState(state, true)` replace — with your store's action functions re-grafted from the live store first, so `useStore(s => s.increment)` keeps working. |
| **Jotai** | Every watched, writable atom is set individually. Read-only atoms are captured for inspection and left to recompute; writable derived atoms may be restored through their write function. |
| **React Query** | Per-query diff against the live cache: existing queries get their state set (observers stay attached, with their real `queryFn`s), missing ones are rebuilt, extras removed. Query data is captured in full — snapshots live on-device, so nothing is trimmed for size — while error objects keep only their name and message. Mutations aren't replayable, so the mutation cache is cleared. |
| **Route** | Opt-in per restore: `router.navigate()` to the captured URL — after the state is applied, or on the next boot when the restore reloads. |

**Limits:** this is *client* state — your backend doesn't time-travel, so a restored cart is only as valid as the server allows. Component-local `useState` and in-flight requests aren't captured. The route restores as a URL (pathname + search params) — the back stack it sat on, and params you passed imperatively as objects rather than in the URL, do not. Sources that can't fully restore say so up front, in the tool.

---

## FAQ

### How do I save and restore app state in React Native while testing?

Install `@buoy-gg/time-machine` and capture a restore point — it snapshots device storage (AsyncStorage, MMKV, SecureStore), Redux, Zustand, Jotai, and the React Query cache together, and restores the whole set in one tap. Set your test state up once and jump back to it every iteration.

### Does restoring reload the app?

Only if you want it to. Live restore swaps state in place — stores are replaced, the query cache is diffed query-by-query so mounted components keep their subscriptions, and the app stays exactly where it is. Restore + reload writes storage back and reloads the JS bundle so in-memory stores rebuild themselves.

### How do I restore the same state over and over without opening the tool?

Tap **Action bar** in the Time Machine header, or swipe a restore point and choose **Bar**. The tool minimizes and a strip stays over the app with that point armed. Each tap on its restore button puts that state back; a long press puts the state back without leaving the screen you are on, and the bar keeps a "Before last restore" copy so you can undo a mis-tap within ten seconds. The strip's `+` asks for a name, captures a new point from wherever you are and arms it, so moving the checkpoint forward takes a name and a tap.

### Do restore points survive an app restart?

Yes — they persist on-device through restarts and JS reloads, and each one also records the route it was captured on so you can return to the screen as well as the state.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
