---
title: React Query DevTools
seoTitle: "React Query DevTools for React Native — inspect the cache"
id: tools-react-query
description: "Full TanStack Query devtools for React Native — browse queries, inspect cached data, simulate loading and error states, and debug fetching on-device."
---

<!-- ::platform-badge platform="both" -->

Inspect the queries in your app's TanStack Query client, including keys, status, observers, and cached data. Edit cache values or simulate loading and errors to test the screen that consumes them.

The demo uses a mock QueryClient. In your app, the tool reads the client supplied by its surrounding provider.

<!-- ::query-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/react-query" yarn="yarn add @buoy-gg/react-query" pnpm="pnpm add @buoy-gg/react-query" bun="bun add @buoy-gg/react-query" -->

Mount `FloatingDevTools` inside the same `QueryClientProvider` as your screens. Installing the package does not make a client outside that context available.

```tsx
<QueryClientProvider client={queryClient}>
  <YourApp />
  <FloatingDevTools />
</QueryClientProvider>
```

This placement example uses your existing `queryClient` and app component; import `QueryClientProvider` from `@tanstack/react-query` and `FloatingDevTools` from `@buoy-gg/core`. Keep your existing account configuration.

Open a screen that runs a query, find its key in the tool, and inspect the cached data. Try a simulated loading state, then restore it and confirm that the screen resumes.

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

Simulate offline mode with one tap. The WiFi toggle controls React Query's `onlineManager` to test offline behavior for queries that honor that manager. It does not disable the device network or override a query's network mode.

---

## What It Can't Do

**The simulated states are cache-level, not network-level.** Triggering a loading state replaces the query function with one that never resolves; it does not slow or block a real request. That is what makes it instant and repeatable — but if you want to see the actual request fail, use a [Network override](./network) instead.

**Mutations are observed, not replayed.** You can read a mutation's variables, status and response, but there is no re-fire button — replaying a mutation would repeat its side effects on your real backend.

## What's Next

- [Network Monitor](./network) — Inspect supported HTTP requests
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
- [Environment Inspector](./env) — Validate env vars with type checking

---

## FAQ

### How do I use React Query devtools in React Native?

Install `@buoy-gg/react-query` and place the menu inside your app's query provider. Open the tool to inspect queries and simulate cache states.

### Does it work with Expo Go?

Yes. It's pure JavaScript — no native modules — so the tool can run in Expo Go. Production access requires Pro and deliberate app authorization.

## Web support (unreleased)

Use the app’s existing QueryClientProvider. The browser host mounts the shared tracker and cache adapter. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
