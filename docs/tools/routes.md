---
title: Route Inspector
seoTitle: "React Native Navigation Debugger — routes, stack & events"
id: tools-routes
description: "Browse every route in your app — Expo Router or React Navigation — jump to any screen, and watch navigation events in real time to debug React Native navigation issues fast."
---

<!-- ::platform-badge platform="both" -->

Inspect captured navigation events, route parameters, and the stack available from your navigator. Use the timeline to compare where navigation started with the screen that became active.

A route duration is time since the previous navigation event, not a measurement of screen render time. The demo uses a mock session.

<!-- ::routes-live-demo -->

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

## What It Can't Do

**It knows the routes your navigator declares.** The sitemap is built from your Expo Router file tree or your React Navigation config, so a screen reached only by an imperative push with an object payload appears in the event stream, but its params are not something the sitemap can predict ahead of time.

**Jumping to a route is navigation, not authorisation.** *Go to route* performs the same navigation your code would. It does not bypass a guard — which is exactly what makes it useful for testing that the guard works.

## What's Next

- [Network Monitor](./network) — Inspect supported HTTP requests
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
- [React Query DevTools](./react-query) — Inspect query cache and simulate states

---

## FAQ

### How do I inspect the navigation stack in React Native?

Install `@buoy-gg/route-events` and open Routes — the Stack tab shows the live stack with route names and params, updating as you navigate.

### Does it work with Expo Router?

Yes — it's built for Expo Router: the sitemap, stack, and event stream all reflect your file-based routes.

## Web support (unreleased)

Browser History API and hash changes feed the shared route history. Register known paths and the framework router adapter for route selection and navigation. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
