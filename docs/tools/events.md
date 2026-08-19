---
title: Events Timeline
seoTitle: "React Native Event Timeline — all app activity in one stream"
id: tools-events
description: "Watch network requests, storage writes, Redux actions, and React Query events in one chronological timeline to debug complex React Native user flows."
---

<!-- ::platform-badge platform="both" -->

See every event across all your dev tools in one chronological timeline. Debug complex user flows by watching network requests, storage changes, Redux actions, and React Query events happen in real-time.

Below is the real tool, running here in your browser on a mock session: a checkout streams in across six tools, `POST /v2/orders` comes back 402, the row opens the Network tool's own page, and the run becomes something you can paste into an AI.

<!-- ::events-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/events" yarn="yarn add @buoy-gg/events" pnpm="pnpm add @buoy-gg/events" bun="bun add @buoy-gg/events" -->

Events Timeline automatically captures from all installed BUOY tools. No additional setup required.

---

## Event Sources

<!-- ::events-sources-grid -->

> **Auto-detection** — If you have the tool installed, its events automatically appear in the timeline.

---

## What You Can Do

<!-- ::events-actions-grid -->

---

## Search

Tap the magnifying glass in the header to filter the timeline as you type. Matches an event's title, its subtitle (status, duration, host, key), and the full URL of network events — so you can search a host, an action type, a storage key, or a query string value.

> Search stacks with the source badges — filter to Network, then search for the failing endpoint.

---

## LLM Export

Copy your event timeline in formats optimized for AI assistants. Reproduce a bug, export, and paste into Claude or ChatGPT with your question.

**Presets:**
- **LLM** — Markdown with smart formatting, strips noise
- **Bug Report** — Includes timestamps and error details
- **JSON** — Machine-readable for integrations
- **Errors** — Just the failures
- **Minimal** — Quick reference, one line per event
- **Diagram** — Mermaid sequence diagram of the flow

The export follows what's on screen: filter to a source or search the timeline first and you copy that slice, not the whole session.

> **Smart formatting** — Automatically parses nested JSON, shows only changed Redux state, and removes verbose fields like image URLs.

---

## Correlation

Related events are linked together. React Query fetch start → success events show as "1/2" badges so you can trace the full lifecycle.

---

## What's Next

- [Network Monitor](./network) — Deep dive into API requests
- [Redux DevTools](./redux) — State inspection and time-travel
- [Storage Explorer](./storage) — Browse and edit persisted data

---

## FAQ

### How do I see what my React Native app did before a bug?

Open the Events timeline — the interleaved history of requests, state changes, storage writes, and navigation leading up to the bug is right there, exportable as structured text.

### What does "LLM-ready export" mean?

The timeline exports in a compact structured format designed to paste into an AI assistant — so the model sees exactly what the app did, in order, with timestamps.
