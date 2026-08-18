---
title: Time Machine
id: tools-time-machine
description: "Snapshot your app's complete client-side state — storage, Redux, Zustand, Jotai, React Query — as named restore points, and jump back in one tap. Test multi-step flows without rebuilding state every iteration."
---

<!-- ::platform-badge platform="both" -->

Testing a checkout flow means building the same state over and over: clear the cart, add ten items, navigate to checkout, test, repeat. Every iteration pays the setup cost again. Reproduced a bug that only happens with specific state? One wrong tap and you're rebuilding it from scratch.

Time Machine is macOS-style restore points for your app's client state: capture **everything** — device storage (AsyncStorage, MMKV, SecureStore), Redux, Zustand, Jotai, and the React Query cache — as a named snapshot, then restore it in one tap. Set up your test state once; jump back to it as many times as you want.

Don't take our word for it — this is the real tool, running right here, loaded with mock restore points. The guided tour below walks one debugging story: a checkout state gets captured, nine things drift, you open the values, see what restore can't apply and why, then restore in reload mode and watch the restore points come back with the app. Or skip the tour and just start tapping:

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

Each state source is captured through the Buoy tool that already watches it — install the ones you use (`@buoy-gg/storage`, `@buoy-gg/redux`, `@buoy-gg/zustand`, `@buoy-gg/jotai`, `@buoy-gg/react-query`) and they register as snapshot sources automatically. The tool shows you exactly which sources can capture and restore, and why when one can't — including a Route tile, which lights up on any Expo Router app and needs no extra package.

---

## What You Can Do

- **Capture a restore point** — name it, and the complete client state persists on-device: it survives app restarts and JS reloads.
- **Restore in one tap** — two modes:
  - **Live restore** swaps state in place: stores are replaced, the query cache is diffed query-by-query (mounted components keep their subscriptions), storage is written back. The app stays exactly where it is.
  - **Restore + reload** restores persisted storage, then reloads the JS bundle — your in-memory stores rebuild themselves from the restored storage. The bulletproof option.
- **Define your own "fresh install"** — set the app up in your clean starting state (logged in, flags set, whatever "fresh" means for you), capture it, and name it. Restore is a *diff*: keys and state that aren't in the snapshot are removed, so restoring your baseline really is your baseline — later junk included. (Need a *true* zero-storage first launch? Agents can create an empty wipe-to-fresh restore point via MCP.)
- **Go back to the screen, not just the state** — every restore point also records the route it was captured on (`/checkout/payment`, query string and all). Restore asks whether to return there too: *State only*, or *Go to `/checkout/payment`*. Pick the route and the app navigates as soon as the state lands — or right after the reload, when you restored in reload mode. Needs Expo Router; without it snapshots simply carry no route and you're never asked.
- **Preview before you restore** — tap any snapshot to see exactly what restoring it will change, item by item: what gets added, removed, overwritten — and what *can't* be applied, with the reason. Tree and split diff views per item, straight from the storage tool's diff UX.
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
