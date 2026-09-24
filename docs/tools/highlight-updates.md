---
title: Highlight Updates
seoTitle: "React Native Re-render Debugger — see why components render"
id: tools-highlight-updates
description: "See why your React Native components re-render — visual overlays highlight every render and reveal the cause: state, props, or parent updates."
---

<!-- ::platform-badge platform="both" -->

See which components render as you interact with a development build. The overlay shows render counts; the detail view shows available causes and hook value changes.

Use the demo to inspect a list update, then compare it with an interaction in your app.

<!-- ::highlight-live-demo -->

## Render Causes

<!-- ::render-causes-grid -->

Inspect the recorded cause alongside the component and interaction before deciding whether a render is unnecessary.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/highlight-updates" yarn="yarn add @buoy-gg/highlight-updates" pnpm="pnpm add @buoy-gg/highlight-updates" bun="bun add @buoy-gg/highlight-updates" -->

That's it. Highlight Updates appears in your FloatingDevTools menu.

---

## What You Can Do

<!-- ::highlight-features-grid -->

---

## Two Modes

Toggle between modes directly from the FloatingDevTools menu:

**Overlay Mode** — Quick visual overlay that shows renders as they happen. Perfect for spotting unnecessary re-renders while you interact with your app.

**Modal Mode** — Full inspector with render history, filtering, and detailed cause breakdowns. Great for deep debugging sessions.

---

## Hook Value Tracking

When a state change causes a render, Highlight Updates shows you the **before and after values** of your hooks. See exactly which `useState` or `useReducer` value changed.

---

## What It Can't Do

**It needs a development build.** Render data comes from React's own DevTools hook (`__REACT_DEVTOOLS_GLOBAL_HOOK__`), which release builds do not install. This tool requires the development hook.

**The overlay costs frame time.** Drawing a box and a counter over every committed component is real work on the UI thread, so the numbers tell you *which* component re-renders and *why*, not what your frame budget looks like with the overlay off. For that measurement use [Bench](./perf-monitor).

## What's Next

- [Image Overlay](./image-overlay) — Overlay design mockups on your running app
- [Environment Inspector](./env) — View and search environment variables
- [Network Monitor](./network) — Inspect supported HTTP requests

---

## FAQ

### How do I find unnecessary re-renders in React Native?

Install `@buoy-gg/highlight-updates` and turn on highlighting — components flash as they render with counts and causes, so over-rendering components and the reason (props, state, parent) are visible immediately.

### How is this different from the React DevTools profiler?

Buoy shows overlays and render details inside the running development build. Use React's profiler for profiling sessions and Buoy for inspecting renders during an interaction. Highlight Updates does not work in production builds.

## Web support (unreleased)

Import @buoy-gg/core/web/register before React DOM to capture roots and renders. The shared inspector measures DOM elements. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
