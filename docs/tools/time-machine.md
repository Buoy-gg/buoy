---
title: Time Machine
seoTitle: "React Native State Snapshots — save & restore app state"
id: tools-time-machine
description: "Snapshot your app's complete client-side state — storage, Redux, Zustand, Jotai, React Query — as named restore points, and jump back in one tap. Test multi-step flows without rebuilding state every iteration."
---

<!-- ::platform-badge platform="both" -->

Testing a checkout flow means building the same state over and over: clear the cart, add ten items, navigate to checkout, test, repeat. Every iteration pays the setup cost again. Reproduced a bug that only happens with specific state? One wrong tap and you're rebuilding it from scratch.

Time Machine is macOS-style restore points for your app's client state: capture **everything** — device storage (AsyncStorage, MMKV, SecureStore), Redux, Zustand, Jotai, and the React Query cache — as a named snapshot, then restore it in one tap. Set up your test state once; jump back to it as many times as you want.

A checkout state gets captured, nine things drift, you open the values, see what restore can't apply and why, then restore in reload mode and watch the restore points come back with the app.

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

- **Capture a restore point** — name it, or don't: an unnamed capture is called after the screen you were on and the time (`/checkout · 14:32`), and you can rename it from the row later. The complete client state persists on-device and survives app restarts and JS reloads.
- **Keep the list yours** — swipe any restore point to duplicate or delete it, and a delete can be undone from the bar that replaces it. Renaming lives on the preview, next to what the point actually contains. The row tells you which point you last restored and when.
- **Restore in one tap** — two modes:
  - **Live restore** swaps state in place: stores are replaced, the query cache is diffed query-by-query (mounted components keep their subscriptions), storage is written back. The app stays exactly where it is.
  - **Restore + reload** restores persisted storage, then reloads the JS bundle — your in-memory stores rebuild themselves from the restored storage. The bulletproof option.
- **Define your own "fresh install"** — set the app up in your clean starting state (logged in, flags set, whatever "fresh" means for you), capture it, and name it. Restore is a *diff*: keys and state that aren't in the snapshot are removed, so restoring your baseline really is your baseline — later junk included. (Need a *true* zero-storage first launch? Agents can create an empty wipe-to-fresh restore point via MCP.)
- **Go back to the screen, not just the state** — every restore point also records the route it was captured on (`/checkout/payment`, query string and all), and "Return to this screen" sits with the other changes in the restore preview. It's on by default and it's a property of the restore point, so you answer once rather than on every restore. The app navigates as soon as the state lands — or right after the reload, when you restored in reload mode. Needs Expo Router; without it snapshots carry no route and the checkbox doesn't appear.
- **Preview before you restore** — tap any snapshot to see exactly what restoring it will change, item by item: what gets added, removed, overwritten — and what *can't* be applied, with the reason. Tap any item and its before/after diff expands inline underneath it — side by side, straight from the storage tool's diff UX, without losing your place in the list.
- **Untick what you don't want back — and it sticks** — every change in the preview has a checkbox. Uncheck the ones a restore should leave alone (that one auth token, a device id, a cache timestamp) and the restore point remembers it immediately, no save step: every later restore skips exactly those items, including the one-tap RESTORE on the list and restores driven from MCP. Everything else still comes back, including keys that only start differing later. Ticking a source's own checkbox toggles everything under it at once.
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
| **Jotai** | Every watched, writable atom is set individually. Derived atoms are captured for inspection and left to recompute. |
| **React Query** | Per-query diff against the live cache: existing queries get their state set (observers stay attached, with their real `queryFn`s), missing ones are rebuilt, extras removed. Query data is captured in full — snapshots live on-device, so nothing is trimmed for size — while error objects keep only their name and message. Mutations aren't replayable, so the mutation cache is cleared. |

| **Route** | Opt-in per restore: `router.navigate()` to the captured URL — after the state is applied, or on the next boot when the restore reloads. |

**Honest limits:** this is *client* state — your backend doesn't time-travel, so a restored cart is only as valid as the server allows. Component-local `useState` and in-flight requests aren't captured. The route restores as a URL (pathname + search params) — the back stack it sat on, and params you passed imperatively as objects rather than in the URL, do not. Sources that can't fully restore say so up front, in the tool.

---

## FAQ

### How do I save and restore app state in React Native while testing?

Install `@buoy-gg/time-machine` and capture a restore point — it snapshots device storage (AsyncStorage, MMKV, SecureStore), Redux, Zustand, Jotai, and the React Query cache together, and restores the whole set in one tap. Set your test state up once and jump back to it every iteration.

### Does restoring reload the app?

Only if you want it to. Live restore swaps state in place — stores are replaced, the query cache is diffed query-by-query so mounted components keep their subscriptions, and the app stays exactly where it is. Restore + reload writes storage back and reloads the JS bundle so in-memory stores rebuild themselves.

### Do restore points survive an app restart?

Yes — they persist on-device through restarts and JS reloads, and each one also records the route it was captured on so you can return to the screen as well as the state.
