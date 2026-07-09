---
title: Console
id: tools-console
description: "Capture every console.log, warn, and error from your React Native app in a Chrome DevTools-style panel — filterable, on-device, no cable or Flipper needed."
---

<!-- ::platform-badge platform="both" -->

A Chrome-DevTools-style console for your React Native app. Buoy Console captures every `console.log`, `console.warn`, `console.error`, and `console.info` on the device and shows them in a familiar, filterable panel — on your phone, on the desktop dashboard, or through your AI agent.

## Installation

<!-- ::PM npm="npm install @buoy-gg/console" yarn="yarn add @buoy-gg/console" pnpm="pnpm add @buoy-gg/console" bun="bun add @buoy-gg/console" -->

Pass the Console preset to `FloatingDevTools`, and mount `ConsoleRoot` once near the app root so capture starts at launch — logs emitted before you open the tool are retained:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { consoleToolPreset, ConsoleRoot } from "@buoy-gg/console";

export default function App() {
  return (
    <>
      {/* your app */}
      <ConsoleRoot />
      <FloatingDevTools apps={[consoleToolPreset]} />
    </>
  );
}
```

It patches `console.*` directly, so logs from your own code and your dependencies are captured — no logger integration or Babel plugin required.

---

## What You Can Do

- **See every log, live** — `log`, `info`, `warn`, and `error` stream in as they happen, color-coded by level.
- **Filter by level** — Focus on just errors and warnings when you're chasing a bug.
- **Search** — Filter messages by substring to find the exact log you care about.
- **Read production logs** — Because Buoy runs on-device in any build, you can read console output from a release build with no cable and no Metro connection.
- **Expand structured data** — Objects and arrays are formatted and expandable, just like the browser console.

---

## Read the console from your AI

With the [MCP server](../mcp), an AI agent can read the console tail directly with `get_console` — filtering by minimum level or message substring to pull just the errors it needs while debugging.

---

## What's Next

- [Events Timeline](./events) — Console logs alongside network, state, and route events
- [Network Monitor](./network) — Inspect the requests behind an error
- [AI / MCP Server](../mcp) — Let an agent read the console for you
