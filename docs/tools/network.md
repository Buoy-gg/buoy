---
title: Network Monitor
seoTitle: "React Native Network Inspector — debug HTTP requests on-device"
id: tools-network
description: "Inspect HTTP requests from supported clients in your React Native app — URLs, headers, timing, and errors — live on the device, no Flipper or proxy required."
---

Inspect HTTP requests on your device: URLs, headers, request and response bodies, timing, and errors. Use development-only overrides to check how your app handles failures without changing the server.

<!-- ::platform-badge platform="both" -->

<!-- ::network-live-demo -->

## Supported Clients

Capture covers requests that pass through the instrumented global `fetch` or `XMLHttpRequest` APIs. Clients using those APIs, including Axios and HTTP-based GraphQL clients, can appear in the inspector. A native client or a separately imported fetch implementation may bypass these hooks.

<!-- ::client-badges-grid -->

## Installation

Set up the core menu and your account using [Quick Start](../quick-start), then install Network if you have not already:

```bash
npm install @buoy-gg/network
```

Restart the development server and app. Open the floating menu and select **Network**. Trigger a new request in your app, then select its row to inspect the response.

In development, interception starts during app initialization. Requests made before the hook is installed or before account access is ready may be missing. An empty list can also mean that your app returned cached data instead of making a new request.

## What You See

A captured request includes its URL, method, status, headers, timing, and available request and response data. Binary or streaming responses may not have a readable body. Inspect the row's details before assuming an empty body means the server returned nothing.

<!-- ::request-fields-grid -->

## Status Colors

Use the status code and error text alongside the row color to distinguish successful requests, failures, and requests still in progress.

<!-- ::status-colors-grid -->

## Override Responses

To check an error state in a development build:

1. Make a request and open its details.
2. Tap **Override** and choose **Server error 500**.
3. Trigger the same request again. Confirm that Network marks it as overridden and that your app displays the expected error state.
4. Disable the rule or the master override switch. Trigger the request again to confirm normal behavior.

The rule starts with the selected request's endpoint, method, status, and response body. If a rule already covers that request, the button opens it.

| Mode | What happens |
| --- | --- |
| Status preset or Custom | Returns the configured status and body without sending the matched request to the server. Presets include 200, 400, 401, 403, 404, 429, 500, and 503. |
| Offline | Simulates a connection failure without sending the matched request. |
| Timeout | Simulates a timeout failure without sending the matched request. |
| Real response | Delays the request, then sends it to the server and returns its real response. Server-side effects still happen. |

These behaviors apply when a supported request is intercepted and its rule matches. Buoy does not block traffic that bypasses its hooks. Use a test environment when exercising mutations.

### Editing the body

The captured **Response Body** is read-only. Change the response returned to your app in the override rule's **Response body** editor. You can edit values, add or delete fields, or use **Edit all** to paste a replacement body into an empty field.

A rule created from a request matches its endpoint, with the query string replaced by a wildcard. Review the pattern before enabling it: requests with different query parameters can match the same rule.

Patterns use `*` globs against the full URL. For example, `*/v1/users*` matches that path on any host. The first enabled rule matching the URL and method wins. Selecting `POST` matches POST requests; it does not change the request method or outgoing body.

Rules persist across app restarts. If they remain armed and untouched across three launches, Buoy pauses them and offers a control to resume them. The master switch disables overrides while retaining the rules.

Overridden requests appear in an `OVERRIDDEN` group and carry a flask icon. The toolbar shows an active-rule count.

Overrides run only in development, skip `OPTIONS` preflights, and exclude Buoy's own license requests. Custom response statuses must be between 200 and 599. A directly imported `expo/fetch` can bypass the global fetch hook. Binary response handling depends on the transport and response type; verify the result in your app before relying on an override for a download.

Free access allows one active override; adding another replaces it. Pro allows up to 50. You can also manage device rules from Desktop or through the `network_override` MCP tool, subject to the connection's account and plan requirements.

## Stepping Between Requests

Use **Previous / Next** in the detail view to move through the current list. Navigation follows the same search, filters, and pinned rows as that list. New requests update the sequence while you read. In the **Saved** view, navigation follows saved requests and their search results.

Lists are newest-first, so **Previous** moves toward newer requests. Body rendering waits briefly while you step through requests; the URL, status, timing, and headers update with each step.

## Pin & Save

The live list holds up to 500 requests. Clearing it or restarting the app removes live history. Use pins or saves to retain a request you want to revisit.

- **Pin** keeps a snapshot in the `PINNED` section above the live list. Pinned requests remain visible regardless of search and filters, and survive Clear and history eviction. Recovery after restart requires a successful storage write.
- **Save** keeps a snapshot in the separate **Saved** list, with its own search and export.

A request can be both pinned and saved. Use the pin or bookmark button in its detail header. You can also long-press a live row to pin it; long-pressing a row in Saved removes the save.

A request pinned while pending continues to update as its status and response arrive. There is a maximum of 25 pins. The Saved list allows 5 entries on Free and 50 on Pro.

Large bodies can be truncated in stored snapshots, and storage budgeting can remove body data. Check the snapshot before relying on it as a complete copy of the payload.

Desktop changes pins and saves on the connected device. Saving there uses the device's snapshot, with the same storage limits.


## What's Next

- [Storage Inspector](./storage): inspect AsyncStorage and registered MMKV instances
- [Environment Inspector](./env): inspect configured environment values
- [React Query](./react-query): inspect query cache and simulate query states

## FAQ

### How do I debug network requests in React Native without Flipper?

Install Buoy's core and Network packages, configure your account, and open Network in the floating menu. Make a request through a supported client and inspect its row. This setup does not require Flipper, a proxy, or a desktop app.

### Does it capture Axios and GraphQL requests?

It captures requests from Axios and HTTP-based GraphQL clients when they use the instrumented fetch or XHR APIs. Supported GraphQL payloads include operation names and variables. Other transports may bypass capture.

### Can I inspect network traffic in a production build?

Production access requires Pro and an app that deliberately exposes the tool to authorized users. Test capture in your target build. Response overrides remain development-only.

## Network conditions (development preview)

The React Native Network tool has four conditions: Normal, Offline (requests), Slow (+500 ms), and Very slow (+2000 ms). An admitted Free or Pro account can select them in a development build. Open **⋯ → Network throttling** to minimize the Network tool and show a compact floating controller. Tap the signal icon to cycle through No throttling, Slow, Very slow and Offline. Changes apply immediately; the icon and delay show the applied profile. The profile text is a label and can be dragged to move the strip. Close restores Normal and dismisses the strip. Drag the background, delay label or grip to move the strip. Buttons keep their tap actions. In React Native development builds, an open strip returns after reload at its saved position, including when hidden at the edge. Close keeps it closed across reloads. The condition resets to No throttling. Tap the grip to hide and restore. Hiding keeps conditions active.

These affect new HTTP(S) calls through global fetch and React Native XHR. Offline rejects them before dispatch; latency adds a wait before dispatch. Conditions do not disconnect Wi-Fi, change NetInfo, or throttle bandwidth. Imported native transports, images and WebSockets may bypass the hooks.

Pending calls retain their starting profile. Offline takes precedence over authored override rules; latency adds to their delay. During active simulation, a finite XHR timeout starts at `send()` and includes artificial waits. Normal keeps the platform's timeout semantics.

Closing the panel, pausing its list, clearing requests or filtering them keeps conditions active. Select No throttling to clear them. A full JS reload or loss of account access resets them automatically. Native simulator and device validation is still pending for this preview.

## Web support (unreleased)

Browser fetch and XHR use the shared capture, rules, conditions, and request panels. CORS still controls which response data the page can read. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.

The capture control reads “Pause network capture” while capture is enabled and “Resume network capture” while it is paused. Existing requests remain visible while capture is paused.
