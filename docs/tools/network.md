---
title: Network Monitor
seoTitle: "React Native Network Inspector — debug HTTP requests on-device"
id: tools-network
description: "Inspect every HTTP request your React Native app makes — URLs, headers, timing, and errors — live on the device, no Flipper or proxy required."
---

<!-- ::platform-badge platform="both" -->

See every API call your app makes. Request, response, headers, timing, errors — all in real-time with zero configuration.

Don't take our word for it — this is the real tool, running right here, loaded with demo traffic. The guided tour below walks one debugging story: traffic streams in, a request fails, you inspect it, force the failure with an override, and pin the evidence. Or skip the tour and just start tapping:

<!-- ::network-live-demo -->

## Supported Clients

<!-- ::client-badges-grid -->

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/network" yarn="yarn add @buoy-gg/network" pnpm="pnpm add @buoy-gg/network" bun="bun add @buoy-gg/network" -->

That's it. The Network Monitor auto-detects and appears in your FloatingDevTools menu. In dev builds, interception installs at app launch — so boot-time traffic (module-scope fetches, session bootstrap, your first queries) is already captured when you open the tool.

---

## What You See

For every request:

<!-- ::request-fields-grid -->

---

## Status Colors

<!-- ::status-colors-grid -->

---

## Stepping Between Requests

Open a request and the detail view gets a **Previous / Next** footer, so comparing two calls no longer means going back to the list and finding your place again.

It steps through exactly what the list was showing — the same pinned rows first, the same filters, the same search. Narrow the list to failures, open one, and Next walks you through the failures only; the counter reads `REQUEST 4 OF 11`, not "4 of everything captured". Requests arriving while you read re-scope it live. Opened from the **Saved** list, it steps through your saves (and their search) instead.

Both lists are newest-first, so **Previous** moves toward the newer request — the same direction as scrolling up.

**It keeps up when you hold it down.** Stepping stays responsive no matter how fast you tap, because the request/response body viewers wait for you to stop before they render — everything else (status, URL, timing, headers) updates on every step. Flick through twenty requests and only the one you land on parses its payload, so the counter never falls behind your thumb. Measured on a 100-request list with 64KB responses, this is the difference between ~650ms and ~15ms of work per tap.

---

## Pin & Save

A failed request has a short life. The list holds the last 500, Clear wipes it, and a reload starts over — so the one call you actually needed is usually gone by the time you go back for it. Two ways to keep it:

**Pin** — hoists the request into a `PINNED` section at the top of the list. Pins ignore your filters and your search, so a pinned request never disappears while you narrow the list around it. A pin is a full snapshot, not a bookmark: it survives Clear, survives being pushed out past the 500-request cap, and survives an app restart.

**Save** — files the request in a separate **Saved** list (the bookmark button in the toolbar), out of the live stream. Same durability, with its own search and export.

The two are independent — a request can be pinned, saved, both, or neither.

**How to use them**

- Open a request and use the **pin** or **bookmark** button in the detail header.
- Or **long-press a row** to pin it. In the Saved list, long-press removes.
- Pinned and saved rows carry a small glyph so you can tell at a glance.

A request pinned while it is still in flight keeps updating — you get its real status and response, not a frozen "Pending".

**On the desktop dashboard** pins and saves are the same list as on the device: toggling one on the dashboard performs it on the device, so both surfaces always agree. Large response bodies are kept intact because the save happens on the device itself.

**Limits.** Up to 25 pins. The Saved list keeps 5 on the free tier and 50 on Pro. Very large request/response bodies are truncated in a saved snapshot so the stored history stays small — everything else about the request is kept.

---

## Override Responses

Chrome DevTools has **Local Overrides** — serve different bytes for a request without touching the server. Buoy has the same idea on device, and goes further: Chrome can't change a status code (override a body there and it forces `200`), and it has no way to simulate latency or a dead connection. Those are the things you actually need on mobile.

Open any request and tap **Override** in the header. That takes you to the rule, prefilled from the request you were looking at — its endpoint, its method, its status, its real response body — so you're never starting from a blank field.

Pick what the app should get instead:

| | What the app sees |
|---|---|
| **Server error 500 / Unauthorized 401 / Not found 404** | That status, with a JSON error body — does your error UI appear? |
| **Forbidden 403 / Rate limited 429 / Unavailable 503 / Bad request 400** | The rest of the failures worth forcing |
| **Success 200** | A 200 with a body you choose — swap in an empty list and check the empty state |
| **Offline** | The exact failure a real dead connection produces |
| **Timeout** | A genuine timeout event, not a generic error |
| **Real response** | Don't change the answer, just make it late |
| **Custom** | Any other status code |

The header button lights up while a rule covers the request you're looking at, and tapping it again opens that rule rather than stacking a second one the first would shadow.

### Editing the body

A request's **Response Body** is read-only — that view answers "what came back". Every edit lives in the rule itself, so there's one place to look and one place to change.

Open a rule's **Response body** and it's an editor and a preview at once: a string renders *as* its input, a number gets steppers, a boolean gets a toggle, every row has a delete. Typing into what you're reading is the edit. It's the same explorer as the [React Query](./react-query) tool, so if you've edited cached query data in Buoy you already know it.

**Edit all** replaces the whole body — it opens an empty field rather than prefilling one, because a real response is routinely hundreds of KB and you only reach for it when you mean to paste something new.

A rule built from a request matches its **endpoint**, not the exact URL — the query string becomes a wildcard. That matters more than it sounds: plenty of clients cache-bust with a timestamp or carry a request id, and pinning the exact URL would give you a rule that silently never fires again.

**Rules survive a reload.** That's the point: force an endpoint to 500, restart the app, and watch what your boot path does with it.

**Matching** uses `*` globs against the whole URL, the same syntax as Chrome's `applyTo` field. An exact URL works as-is; `*/v1/users*` matches that path on any host. Rules are an ordered list and the first enabled match wins.

The method is part of the **match**, not the target. Picking `POST` means "when the app POSTs here, hand it this response" — overrides always replace what the app *receives*, and never touch the body you send. Mutation responses are worth forcing for exactly that reason: make a checkout POST return 500, or a PATCH come back with a validation error.

**It says so, loudly.** Overridden requests are hoisted into an `OVERRIDDEN` strip at the top of the list, rows carry a flask glyph, and the toolbar button lights up with a count.

**And it lets go.** Rules surviving a reload is the point — but a body your app can't render would otherwise re-break it on every launch, with the controls to undo it locked inside an app that no longer draws. So if overrides sit armed and untouched across three launches, they pause themselves: `Overrides paused — they'd been on for 3 launches`, with one tap to turn them back on. Force-a-500-and-restart still works; a wedge can't outlive it.

**Safety.** Overrides only run in development builds, never touch Buoy's own licence traffic, and skip `OPTIONS` preflights. One master switch turns everything off without losing your rules.

**From the dashboard and from AI.** Rules live on the device, but you can author them from the desktop dashboard, and the `network_override` MCP tool lets an agent force a state and then check what the app rendered — "make this endpoint 500 and tell me what the user sees."

**Limits.** Free is one override at a time — a new one replaces the live one rather than being refused. Pro runs up to 50. Status codes must be 200–599 (for connection-level failures use **Offline**, which is what a real one looks like). Requests made with `expo/fetch` imported directly, and binary `arraybuffer` downloads, are not overridden.

---

## What's Next

- [Storage Inspector](./storage) — Browse and edit AsyncStorage & MMKV
- [Environment Inspector](./env) — Validate env vars with type checking
- [React Query](./react-query) — Inspect query cache and simulate states

---

## FAQ

### How do I debug network requests in React Native without Flipper?

Install `@buoy-gg/network` and open the floating menu — requests appear live on the device. Flipper was deprecated in RN 0.73; Buoy needs no native SDK, proxy, or desktop app.

### Does it capture Axios and GraphQL requests?

Yes — fetch, Axios, and GraphQL (including operation names and variables) are captured automatically, plus gRPC-web.

### Can I inspect network traffic in a production build?

Yes. The inspector ships inside the app, so authorized users can open it in staging and production builds — where desktop-tethered tools can't attach.
