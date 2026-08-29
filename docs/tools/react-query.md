---
title: React Query DevTools
seoTitle: "React Query DevTools for React Native — inspect the cache"
id: tools-react-query
description: "Full TanStack Query devtools for React Native — browse queries, inspect cached data, simulate loading and error states, and debug fetching on-device."
---

<!-- ::platform-badge platform="both" -->

React Query bugs are almost never in the request. The network tab shows a clean 200 and the screen still shows yesterday's data, because the problem is in the cache: which key it landed under, whether the query is stale, whether anything is still observing it.

TanStack's own devtools answer exactly that — and they are web-only, so on React Native you are back to logging `queryClient.getQueryData` and rebuilding to read it. Buoy puts the same query browser on the device: every query with its key, state and cached data, editable in place, plus one-tap loading and error simulation.

The demo below is the real on-device tool on a mock QueryClient: the cache lights up, a checkout query fails, you inspect cart JSON, force a loading spinner without the network, restore it, then free-play.

<!-- ::query-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/react-query" yarn="yarn add @buoy-gg/react-query" pnpm="pnpm add @buoy-gg/react-query" bun="bun add @buoy-gg/react-query" -->

That's it. The React Query DevTools auto-detects your QueryClient and appears in your FloatingDevTools menu.

---

## Query States

<!-- ::query-states-grid -->

---

## What You Can Do

<!-- ::query-actions-grid -->

> **Simulate loading & error states** — Test how your UI handles loading spinners and error boundaries without waiting for real network conditions.

---

## Mutations

Track all your mutations in real-time:

- **Status** — idle, pending, success, or error
- **Variables** — data passed to the mutation
- **Response** — returned data or error message
- **Timing** — when the mutation was submitted

---

## WiFi Toggle

Simulate offline mode with one tap. The WiFi toggle controls React Query's `onlineManager` to pause all queries — perfect for testing offline-first features.

---

## What It Can't Do

**The simulated states are cache-level, not network-level.** Triggering a loading state replaces the query function with one that never resolves; it does not slow or block a real request. That is what makes it instant and repeatable — but if you want to see the actual request fail, use a [Network override](./network) instead.

**Mutations are observed, not replayed.** You can read a mutation's variables, status and response, but there is no re-fire button — replaying a mutation would repeat its side effects on your real backend.

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
- [Environment Inspector](./env) — Validate env vars with type checking

---

## FAQ

### How do I use React Query devtools in React Native?

The official TanStack devtools are web-only. Install `@buoy-gg/react-query` and the same capabilities — query browser, cache inspector, state simulation — run inside your app on the device.

### Does it work with Expo Go?

Yes. It's pure JavaScript — no native modules — so it works in Expo Go, dev builds, and production.
