---
title: Tools
seoTitle: "Buoy tools for .NET MAUI"
id: maui-tools
description: "Set up each MAUI tool. See what it reads and where it differs from React Native."
---

Each tool has its own page.
It shows the setup call and what the tool can and can't see.

`builder.UseBuoy()` turns on most tools by itself.
Routes, Scenarios, Impersonate, Notifications, Sentry and Ask Buoy need their own call.
Call a tool's setup before `UseBuoy()` to give it options.

- [Network](./tools/network): See web calls, headers, bodies, status, and timing.
- [Env](./tools/env): See app values and check them against your rules.
- [Storage](./tools/storage): Read, edit, and check known app keys.
- [Events](./tools/events): See web calls and store events in one list.
- [Console](./tools/console): See app logs with search and level filters.
- [Clock](./tools/clock): Set a date or jump ahead.
- [Impersonate](./tools/impersonate): Test your app as another user.
- [Perf Monitor](./tools/perf-monitor): See how fast your app runs, and compare runs.
- [Images](./tools/images): See each image your app loads, and find big or broken ones.
- [Image Overlay](./tools/image-overlay): Lay a design picture over your app to check your work.
- [Debug Borders](./tools/debug-borders): Draw a box around each view, and tap a tag to see its id.
- [Time Machine](./tools/time-machine): Save your app's state and put it back later.
- [Assets](./tools/assets): See every image, font and file your app ships.
- [Sentry](./tools/sentry): See what your app sends to Sentry, and what it dropped.
- [Scenarios](./tools/scenarios): Set up a test state with one tap, then undo it.
- [Notifications](./tools/push-notifications): Send a test notification and see what your app got.
- [Lifecycle](./tools/lifecycle): Test app states, themes, links, and power values.
- [Routes](./tools/routes): See your page stack and the route log.
- [Location](./tools/location): Test a place, route, weak signal, or coarse fix.
- [Permissions](./tools/permissions): Test how your app reacts to each grant state.

For in-app chat, see [Ask Buoy](./ask-buoy).
To add your own tool, see [Custom tools](./custom-tools).
