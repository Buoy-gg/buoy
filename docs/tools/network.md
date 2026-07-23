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

## What's Next

- [Storage Inspector](./storage) — Browse and edit AsyncStorage & MMKV
- [Environment Inspector](./env) — Validate env vars with type checking
- [React Query](./react-query) — Inspect query cache and simulate states
