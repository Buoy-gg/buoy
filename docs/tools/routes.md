---
title: Route Inspector
id: tools-routes
description: "Browse every route in your app — Expo Router or React Navigation — jump to any screen, and watch navigation events in real time to debug React Native navigation issues fast."
---

<!-- ::platform-badge platform="both" -->

See every route in your app and track navigation in real-time. Browse your sitemap, jump to any screen, and debug navigation issues instantly.

<!-- ::routes-demo -->

## Supported Libraries

<!-- ::nav-libraries-grid -->

> **Auto-detection** — The Route Inspector automatically detects which navigation library you're using and adapts accordingly.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/route-events" yarn="yarn add @buoy-gg/route-events" pnpm="pnpm add @buoy-gg/route-events" bun="bun add @buoy-gg/route-events" -->

That's it. The Route Inspector auto-detects your navigation setup and appears in your FloatingDevTools menu.

---

## What You Can Do

<!-- ::route-features-grid -->

---

## Route Types

<!-- ::route-types-grid -->

---

## Event Timeline

Every navigation is tracked with:

- **Path** — Where you navigated to
- **Params** — Route parameters passed
- **Timestamp** — When it happened
- **Duration** — Time since previous navigation

Tap any event to open its **detail page** — the full route template, from/to paths, timing, segments, and params, all copyable, plus a **Go to route** action to jump straight there. This matches how events open in the Events tool.

---

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
- [React Query DevTools](./react-query) — Inspect query cache and simulate states
