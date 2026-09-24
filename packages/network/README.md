# @buoy-gg/network

[![npm version](https://img.shields.io/npm/v/@buoy-gg/network?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/network) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/network?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/network)

Inspect HTTP requests captured through your React Native app’s global `fetch` and `XMLHttpRequest` hooks. Open a request to see its URL, status, headers, timing, and captured body.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

![Network Monitor demo](https://github.com/user-attachments/assets/473ddf83-03cd-4bd1-8dc3-0f66eda9fa8a)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/network
```

## Before you start

Use a development build with `@buoy-gg/core` and a Free or Pro Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

## Quick start

After initialization, render the menu in your app root:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />
    </>
  );
}
```

Want a different label or color? Build the tool with `createNetworkTool({ name, colorPreset })` and pass it via `apps={[...]}`.

## Check the integration

Open Network, trigger a new request in your app, and inspect its URL and status. Requests made before instrumentation and account admission, or through a separate native transport, may not appear. A cache hit may make no request. Saved bodies retain capture limits; saving does not recover truncated data.

## What you get

- **Supported requests** — intercepts global `fetch` and `XMLHttpRequest`. Clients using those paths can be captured; separate native transports and previously saved references may bypass the hooks.
- **Early capture** — development instrumentation can capture before the panel opens, once its hooks and account admission are ready. It cannot recover earlier requests.
- **GraphQL gets special treatment** — operation names are extracted from queries, mutations, and subscriptions and shown with variables in arrow notation: `GetUser › 123`. No more guessing which `/graphql` request is which.
- **Full request detail** — request/response headers, payloads, HTTP status, and duration for captured calls, with status-colored rows.
- **Filter and search** — narrow by status code, HTTP method, or URL pattern; search captured events; pause/resume capture; clear history.
- **Pin the one that failed** — pin a request to hold it at the top of the list (ignoring your filters), or save it to a separate favorites list. Both keep the captured snapshot, including any body truncation, so it survives Clear and the 500-request cap. Restart recovery requires a successful storage write. Long-press a row, or use the buttons in the request detail header.
- **Step between requests** — the detail view has a Previous/Next footer that walks exactly what the list was showing (same pins, filters, and search — or your Saved list), so comparing two calls doesn't mean going back and finding your place.
- **Ignore the noise** — a shared ignored-domains/URL filter (`useIgnoredPatterns`) hides endpoints you never want to see, shared with the Events tool.
- **Hooks for custom UIs** — `useNetworkEvents`, `useNetworkEventList`, `useNetworkBadgeCounts`, and `useNetworkSaved` expose the capture stream with narrow per-consumer subscriptions.


## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/network) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Network conditions

In development builds, open **⋯ → Network throttling** to minimize Network and show compact controls over the app. Restore Network from its minimized icon. Tap the signal icon to cycle through No throttling, Slow, Very slow and Offline. Each tap applies the profile immediately and updates the icon and delay. The profile text is a label and can be dragged to move the strip. Close restores Normal and dismisses the controls. Drag the background, delay label or grip to move the strip. Buttons keep their tap actions. In React Native development builds, an open strip returns after reload at its saved position, including when hidden at the edge. Close keeps it closed across reloads. The condition resets to No throttling. Tap the grip to hide or restore it. Free and Pro accounts can use Network conditions after account admission.

Offline rejects new HTTP(S) calls through the installed global fetch and React Native XHR hooks without sending them. Slow and Very slow add one delay before dispatch. They do not limit bandwidth or change device connectivity. Imported native transports, images and WebSockets can bypass these hooks.

Each call keeps the profile selected when it starts. Offline takes precedence over authored overrides without consuming their counters. Latency adds to an authored delay. A finite XHR timeout under active conditions covers the whole call from `send()`, including artificial waits; Normal preserves native timeout behavior.

Conditions remain active when the panel is closed or its list is paused. They reset on a full JS reload or account access loss and are never persisted. Captured requests retain their condition stamp for saved snapshots, detail views and export. Desktop and MCP can control a connected device that advertises the conditions actions.

Network conditions are a development preview. The native compatibility matrix across transports, runtimes and devices is still being worked through, so confirm the behaviour you rely on in your own build before you depend on it.

## Web support (unreleased)

Browser fetch and XHR use the shared capture, rules, conditions, and request panels. CORS still controls which response data the page can read. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.

The capture control reads “Pause network capture” while capture is enabled and “Resume network capture” while it is paused. Existing requests remain visible while capture is paused.
