---
title: React Query DevTools
seoTitle: "React Query DevTools for React Native — inspect the cache"
id: tools-react-query
description: "Full TanStack Query devtools for React Native — browse queries, inspect cached data, simulate loading and error states, and debug fetching on-device."
---

<!-- ::platform-badge platform="both" -->

Full TanStack Query inspection for React Native. Browse queries, view cached data, simulate states, and debug your data fetching in real-time.

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
