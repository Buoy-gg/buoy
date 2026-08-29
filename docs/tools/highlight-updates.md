---
title: Highlight Updates
seoTitle: "React Native Re-render Debugger — see why components render"
id: tools-highlight-updates
description: "See why your React Native components re-render — visual overlays highlight every render and reveal the cause: state, props, or parent updates."
---

<!-- ::platform-badge platform="both" -->

You already suspect the list is re-rendering too much. What you cannot get is *why* — and why is the only part that tells you what to change.

React's profiler records a session for a desktop to analyse afterwards; it will not tell you that this card re-rendered because a parent passed a new inline object while you were typing in a search box. Highlight Updates draws the renders on the running app as they happen, counts them per component, and names the cause on each one — down to the `useState` value before and after.

Typing in a search box re-renders every card in the list, the 47x badge names the worst offender, the detail view names the cause — down to the exact useState before → after.

<!-- ::highlight-live-demo -->

## Render Causes

<!-- ::render-causes-grid -->

> **Know the WHY** — Every render is tagged with its cause. No more guessing why your component updated.

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

**It needs a development build.** Render data comes from React's own DevTools hook (`__REACT_DEVTOOLS_GLOBAL_HOOK__`), which release builds do not install. Most Buoy tools run in production; this one genuinely cannot.

**The overlay costs frame time.** Drawing a box and a counter over every committed component is real work on the UI thread, so the numbers tell you *which* component re-renders and *why*, not what your frame budget looks like with the overlay off. For that measurement use [Bench](./perf-monitor).

## What's Next

- [Image Overlay](./image-overlay) — Overlay design mockups on your running app
- [Environment Inspector](./env) — View and search environment variables
- [Network Monitor](./network) — See every API call your app makes

---

## FAQ

### How do I find unnecessary re-renders in React Native?

Install `@buoy-gg/highlight-updates` and turn on highlighting — components flash as they render with counts and causes, so over-rendering components and the reason (props, state, parent) are visible immediately.

### How is this different from the React DevTools profiler?

The profiler records a session for later analysis on a desktop. Buoy highlights renders live on the device while you use the app — and also works in staging/production builds where the profiler can't attach.
