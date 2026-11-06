---
title: Network Monitor
seoTitle: "React Native Network Inspector — debug HTTP requests on-device"
id: tools-network
description: "Inspect HTTP requests from supported clients in your React Native app — URLs, headers, timing, and errors — live on the device, no Flipper or proxy required."
---

Inspect HTTP requests on your device: URLs, headers, request and response bodies, timing, and errors. Use overrides to check how your app handles failures without changing the server.

<!-- ::platform-badge platform="both" -->

<!-- ::tool-film id="network" -->

<!-- ::network-live-demo -->

Buoy hides its own cloud settings calls from the list.

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

To check an error state:

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

Patterns use `*` globs against the full URL. For example, `*/v1/users*` matches that path on any host. A pattern that starts with `/` is a path on any host: `/v1/users` matches `https://api.example.com/v1/users`, and `/v1/users*` also matches it with a query string. The first enabled rule matching the URL and method wins. Selecting `POST` matches POST requests; it does not change the request method or outgoing body.

Rules persist across app restarts. If they remain armed and untouched across three launches, Buoy pauses them and offers a control to resume them. In a browser, each new tab counts as a launch and reloading the same tab doesn't, so rules keep working while you reload. The master switch disables overrides while retaining the rules.

Overridden requests appear in an `OVERRIDDEN` group and carry a flask icon. The toolbar shows an active-rule count.

Overrides skip `OPTIONS` preflights, and exclude Buoy's own license requests. A release build needs a paid plan and the Buoy menu on screen. Custom response statuses must be between 200 and 599. A directly imported `expo/fetch` can bypass the global fetch hook. Binary response handling depends on the transport and response type; verify the result in your app before relying on an override for a download.

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


## Sockets

Buoy shows each WebSocket your app opens. That covers chat, live updates and any other socket. Open **Sockets** from the ⋯ menu. A button also shows up in the bar once your app opens a socket. It turns green while one is open.

### What you see

- A card for each open socket. It says how long it has been up. If it came after failed tries, it says which try this is. With no socket open, you see the last one that closed.
- Each message, newest first. ↑ means your app sent it. ↓ means your app got it.
- Open and close lines between the messages. You can see where a socket dropped and came back.
- Pings and pongs fold into one heartbeat count on the card. Tap the count to show them.
- A socket that never opened and closed with 1006 says "couldn't connect". One that opened first says "lost connection".
- Three or more fast repeats fold into one row, like `chat.typing ×18`. Tap the row to see each one.
- A chat row shows its text first, like `"hey!"`.

Tap a message to see all of it. If it was a request, its reply shows below it, with the time the reply took.

Buoy reads each message's name from `type`, `event`, `op`, `action` or `method`. It pairs a request with its reply by `requestId`, `id` or `ref`. It reads socket.io and Phoenix Channels messages too.

Socket opens and closes also show in [Events](./events). Desktop shows the same Sockets screen for the device you pick.

### Problems Buoy finds

Rows with a problem turn red. Problems from the last 2 minutes also show in a red bar at the top, with the time.

- **No reply.** A request got no reply in 10 seconds. Buoy only checks message types that got a reply before. So a message the server never answers, like typing, is never flagged. If the socket closes first, the close row tells you why.
- **Reconnect storm.** A socket opened more than 3 times in 15 seconds.
- **Sign-in loop.** Sign-in failed 3 times in 2 minutes. That means a close with code 1008, or a message like `auth.expired`.
- **Can't connect.** 3 tries in a row closed before they opened. The server may be down.
- **Two sockets.** Two sockets are open to the same URL at once.
- **Sent while closed.** Your app sent a message on a socket that was not open.

### Tricks

Tricks break the socket on purpose. Your app's real code handles each one. Tricks need Pro.

- **Push to app** hands your app a message as if the server sent it. Try `{"type":"auth.expired"}`.
- **Send to server** sends a message on the socket.
- **Drop it** closes with code 1006, like lost signal. On iOS, a real lost link often closes as 1001 instead. So check for both.
- **Kick out** closes with code 1008, like a bad token.
- **Server going away** closes with code 1001, like a deploy.
- **Freeze** keeps the socket looking open, but nothing gets through. It tests your ping check.
- **Block** drops open sockets, and each new one fails. It tests your backoff.

Buoy marks every fake message. **Reset all** turns Freeze and Block off. Network's offline mode drops sockets too.

### Secrets

Buoy hides secrets before it keeps a message. That means fields like `token` and `password`, any JWT, and the payload of a sign-in message. They show as `[hidden]`. If you send that message again, `[hidden]` is what goes out.

### Limits

- Buoy sees sockets made after the Network tool loads. In dev builds, that is at app start.
- It keeps the last 1,000 messages. A message over 64 KB is cut.
- It watches the JavaScript `WebSocket`. Sockets made in native code are not seen.
- The real ping and pong frames never reach JavaScript. Buoy sees only your app's own ping messages.
- Metro, React DevTools and Buoy's own sockets are left out.

### Use MCP

`get_sockets` lists the open sockets, the last few closed ones, the problems and the last 50 messages. Add `type: "chat.*"` or `problems: true` to narrow it down. `includeBodies: true` shows up to 4,000 characters of each message. For one whole message, pass its `frameId`.

`socket_action` runs the tricks:

```json
{ "action": "inject", "data": { "type": "auth.expired" } }
```

The actions are `inject`, `send`, `close`, `freeze`, `unfreeze`, `block`, `unblock`, `reset` and `clear`. When one socket is open, you can leave out `socketId`.

## What's Next

- [Storage Inspector](./storage): inspect AsyncStorage and registered MMKV instances
- [Environment Inspector](./env): inspect configured environment values
- [React Query](./react-query): inspect query cache and simulate query states

## FAQ

### How do I debug network requests in React Native without Flipper?

Install Buoy's core and Network packages, configure your account, and open Network in the floating menu. Make a request through a supported client and inspect its row. This setup does not require Flipper, a proxy, or a desktop app.

### Does it capture Axios and GraphQL requests?

It captures requests from Axios and HTTP-based GraphQL clients when they use the instrumented fetch or XHR APIs. Supported GraphQL payloads include operation names and variables. Other transports may bypass capture.

### Does it show WebSockets?

Yes. Open **Sockets** from the ⋯ menu. It shows each socket, its messages and the problems Buoy finds. See [Sockets](#sockets).

### Can I inspect network traffic in a production build?

Production access requires Pro and an app that deliberately exposes the tool to authorized users. Test capture in your target build.

### Do overrides work in a release build?

Yes, in React Native, Swift, Android and .NET MAUI. Overrides and slow or offline modes work with a paid plan while the Buoy menu is on screen. An amber FAKE NETWORK pill shows the whole time. Press and hold it to stop.

Nothing is faked with no paid plan, or with no menu, as in headless mode. A web call that a rule matches waits up to 3 seconds for the menu when the app starts. A direct `XMLHttpRequest` at that moment does not wait, so it goes out for real.

Offline also reaches sockets in a release build. New ones fail. One that Buoy already watches closes. One opened before Buoy started watching stays open.

## Network conditions (preview)

Use **⋯ → Network throttling** to open the small strip.
Tap its signal icon to cycle through the four old modes.
These are No throttling, Slow, Very slow, and Offline.
Use **⋯ → Speed and link state** for the new modes.
The strip opens with the full controls shown.
Tap Done to hide the controls. Tap Edit to show them.
The strip shows the active speed. Drag it to move it.
On Android, drag by the grip while you edit.
Close clears both controls. Hide keeps them on.
The strip keeps its place on reload. The speed resets.

Calls keep the speed that was set when they began.
Offline wins over mock rules. Speed waits add to rule waits.
With a speed set, XHR timeouts count from `send()`.
This includes the wait and all paced byte reads.
Normal keeps the app's own timeout rules.
Pause, clear, and filter keep the test state on.
Loss of account access clears both controls.
See [Test a slow link](#test-a-slow-link) for scope.

## Web support

Browser fetch and XHR use the shared capture, rules, conditions, and request panels. CORS still controls which response data the page can read. Import it from the package's `/web` entry (7.0.41 or later). See [Web installation](../web/installation#tool-setup) for registration, dependencies, and browser boundaries.

The capture control reads “Pause network capture” while capture is enabled and “Resume network capture” while it is paused. Existing requests remain visible while capture is paused.

## Native iOS (Swift)

Network Monitor is also part of [Buoy for Swift](../swift/overview) for SwiftUI and UIKit apps. Setup and what the Swift version covers are in the [Swift tools guide](../swift/tools#network).

Buoy keeps its own web calls out of this view. App calls still show when they use the same files.

## Test a slow link

Use a dev build, or a release build with a paid plan and the Buoy menu on screen. Sign in with your account.
Use the app, Desktop, or MCP to set them.
App reload clears them. No throttling adds no byte wrapper or cap timer.
Normal capture still has its own cost. No throttling clears both controls.
The chip stays on while a test state is set.

| Speed | Added wait | Down bytes/s | Up bytes/s |
| --- | --- | ---: | ---: |
| Slow 3G | 2000 ms | 50000 | 50000 |
| Slow 4G | 562.5 ms | 180000 | 84375 |
| Fast 4G | 165 ms | 1012500 | 168750 |

Down caps pace byte reads to the app in JS.
Up caps wait before send, based on body bytes.
For gzip, down caps count unpacked body bytes.
Upload time is simulated. The socket is not paced.
XHR upload progress shows that simulated wait where supported.
Each call has its own cap. Slow and Very slow only add waits.
Text, bytes and Blob uploads work with the speed caps.
FormData adds a wait for the parts we can size.
This includes text, bytes, Blob, and files with a `size`.
Parts with no known size pass through with latency only.
Multipart headers and boundaries are not counted.
Streams pass through with latency only, without being read first.
The list, detail, and MCP show each cap gap.
They say `upload cap partial: size unknown` when some bytes count.
With no known bytes, they say `upload cap not applied: size unknown`.

| Path | What changes |
| --- | --- |
| Global fetch | Offline, waits, and paced JS body reads. |
| RN XHR and Axios XHR | Offline, waits, progress, and timed final load. |
| FormData uploads | Wait for known parts; mark the cap as partial. |
| Upload streams or files with no known size | Latency only for those bytes; show the cap gap. |
| Browser XHR | Offline and waits. RN byte hooks are needed for caps. |
| WebSocket | Nothing, except in Offline. |
| Native images, SDKs and file transfers | Nothing. |
| Direct `expo/fetch` or saved native fetch refs | May bypass the hooks. |
| NetInfo and Expo Network | App link state, with the early hook. |

Native code may hold bytes before the app reads them.
This tool does not model packet loss or change radios.
Cached app data may still show when calls fail.

### Change what the app thinks

In the dev entry, load the peer's hook before app imports:

```ts
import '@buoy-gg/network/netinfo';
// Use this too if the app uses Expo Network:
import '@buoy-gg/network/expo-network';
```

Use only hooks whose peers are already in the app.
Tested peers are NetInfo 12 and Expo Network 56.
These hooks add no native code. No app rebuild is needed.
Late setup shows a gap. Fix the order, then reload.

Choose Real link, Offline, Wi-Fi, Cellular, or No internet.
No internet means Wi-Fi with no internet access.
Follow speed uses Offline when the speed is Offline.
Otherwise it uses the real state. Web calls stay separate.
Restore emits the latest real state, even if still offline.

NetInfo covers `fetch`, `refresh`, `addEventListener`, and `useNetInfo`.
Isolated `useNetInfoInstance` is outside the tested scope.
Expo covers `getNetworkStateAsync`, `addNetworkStateListener`, and `useNetworkState`.
IP and airplane-mode reads stay real.

### Use MCP

```json
{ "action": "set", "profile": "slow3g", "connection": "cellular" }
```

Send this to `network_conditions`. Read with `action: "get"`.
Use `deviceId` to choose a device when needed.
Set needs at least one of `profile` and `connection`.
Profiles are `normal`, `offline`, `slow`, `verySlow`, `slow3g`, `slow4g`, `fast4g`.
Link states are `auto`, `real`, `offline`, `wifi`, `cellular`, `noInternet`.
A profile-only `normal` clears both controls for old clients.
An explicit link state can be set with `normal`.
The device sends its state and rates. It shows hook support and scope too.
Desktop uses that same state and checks it on reconnect.
