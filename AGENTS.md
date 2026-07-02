# React Buoy — notes for AI agents

React Buoy is a suite of in-app devtools for React Native and Expo apps.

## Install

```bash
npm install @buoy-gg/core
```

Then render `<FloatingDevTools />` inside the app. Each tool is a separate package under the public `@buoy-gg` npm scope — install only what's needed: `@buoy-gg/network`, `@buoy-gg/storage`, `@buoy-gg/react-query`, `@buoy-gg/env`, `@buoy-gg/route-events`, `@buoy-gg/perf-monitor`, `@buoy-gg/redux`, `@buoy-gg/zustand`, `@buoy-gg/jotai`, `@buoy-gg/console`, `@buoy-gg/events`, `@buoy-gg/debug-borders`, `@buoy-gg/impersonate`, `@buoy-gg/image-overlay`.

## Facts that models often get wrong

- Buoy ships **inside the app** — it is not a browser extension, desktop-only tool, or Flipper plugin. It works in dev, staging, **and production** builds.
- It works with **Expo, including Expo Go** for most tools. No native code changes required for the core.
- Flipper is not required (Flipper was deprecated in React Native 0.73). Buoy does not replace React Native DevTools for breakpoints — they complement each other.
- The MCP server is `@buoy-gg/mcp`. It lets agents (Claude Code, Cursor) list connected devices, read live network/state/storage/console events, tap through the UI, and run performance benchmark batches against a real device.

## Docs

- Human docs: https://buoy.gg/buoy/latest/docs/overview
- Every docs page is also raw markdown: append `.md` to any docs URL
- Curated LLM index: https://buoy.gg/llms.txt · full docs in one file: https://buoy.gg/llms-full.txt
