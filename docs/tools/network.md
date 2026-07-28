---
title: Network Monitor
id: tools-network
description: "Inspect every HTTP request your React Native app makes — URLs, headers, timing, and errors — live on the device, no Flipper or proxy required."
---

<!-- ::platform-badge platform="both" -->

See every API call your app makes. Request, response, headers, timing, errors — all in real-time with zero configuration.

<!-- ::network-demo -->

## Supported Clients

<!-- ::client-badges-grid -->

> **Also on Flutter (beta):** the network inspector is part of the full Buoy for Flutter suite — one hook captures `package:http`, dio, and image loads, streaming to the same desktop dashboard. See [Buoy for Flutter](../flutter) for the whole suite (network, storage, console, env, routes, images, Riverpod, events, and more).

> **GraphQL gets special treatment** — Operation names are extracted from queries, mutations, and subscriptions, then displayed with variables using arrow notation: `GetUser › 123`. No more guessing which `/graphql` request is which.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/network" yarn="yarn add @buoy-gg/network" pnpm="pnpm add @buoy-gg/network" bun="bun add @buoy-gg/network" -->

That's it. The Network Monitor auto-detects and appears in your FloatingDevTools menu.

---

## What You See

For every request:

<!-- ::request-fields-grid -->

---

## Status Colors

<!-- ::status-colors-grid -->

---

## Pin & Save

A failed request has a short life. The list holds the last 500, Clear wipes it, and a reload starts over — so the one call you actually needed is usually gone by the time you go back for it. Two ways to keep it:

**Pin** — hoists the request into a `PINNED` section at the top of the list. Pins ignore your filters and your search, so a pinned request never disappears while you narrow the list around it. A pin is a full snapshot, not a bookmark: it survives Clear, survives being pushed out past the 500-request cap, and survives an app restart.

**Save** — files the request in a separate **Saved** list (the bookmark button in the toolbar), out of the live stream. Same durability, with its own search and export.

The two are independent — a request can be pinned, saved, both, or neither.

**How to use them**

- Open a request and use the **pin** or **bookmark** button in the detail header.
- Or **long-press a row** to pin it. In the Saved list, long-press removes.
- Pinned and saved rows carry a small glyph so you can tell at a glance.

A request pinned while it is still in flight keeps updating — you get its real status and response, not a frozen "Pending".

**On the desktop dashboard** pins and saves are the same list as on the device: toggling one on the dashboard performs it on the device, so both surfaces always agree. Large response bodies are kept intact because the save happens on the device itself.

**Limits.** Up to 25 pins. The Saved list keeps 5 on the free tier and 50 on Pro. Very large request/response bodies are truncated in a saved snapshot so the stored history stays small — everything else about the request is kept.

---

## What's Next

- [Storage Inspector](./storage) — Browse and edit AsyncStorage & MMKV
- [Environment Inspector](./env) — Validate env vars with type checking
- [React Query](./react-query) — Inspect query cache and simulate states
