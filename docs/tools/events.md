---
title: Events Timeline
id: tools-events
---

<!-- ::platform-badge platform="both" -->

See every event across all your dev tools in one chronological timeline. Debug complex user flows by watching network requests, storage changes, Redux actions, and React Query events happen in real-time.

<!-- ::events-demo -->

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

## LLM Export

Copy your event timeline in formats optimized for AI assistants. Reproduce a bug, export, and paste into Claude or ChatGPT with your question.

**Presets:**
- **LLM** — Markdown with smart formatting, strips noise
- **Bug Report** — Includes timestamps and error details
- **JSON** — Machine-readable for integrations
- **Errors Only** — Just the failures

> **Smart formatting** — Automatically parses nested JSON, shows only changed Redux state, and removes verbose fields like image URLs.

---

## Correlation

Related events are linked together. React Query fetch start → success events show as "1/2" badges so you can trace the full lifecycle.

---

## What's Next

- [Network Monitor](./network) — Deep dive into API requests
- [Redux DevTools](./redux) — State inspection and time-travel
- [Storage Explorer](./storage) — Browse and edit persisted data
