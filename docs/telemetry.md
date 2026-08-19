---
title: Telemetry
seoTitle: "Buoy Telemetry — what the anonymous install ping sends, and how to turn it off"
id: telemetry
description: "Buoy sends one anonymous ping per day in development. Exactly what it contains, what it never contains, and how to disable it in one line."
---

Buoy sends **one anonymous ping per day, in development only**. This page is the
complete description of it — there is nothing else, and no other network call
Buoy makes on its own behalf.

## Turn it off

```ts
import { Buoy } from '@buoy-gg/core';

Buoy.init({
  licenseKey: process.env.BUOY_KEY,
  telemetry: false,
});
```

That's the whole opt-out. When it's off, nothing is sent and nothing is written
to disk — opting out really is opting out, not "collected but not transmitted".

## What it sends

Four fields. That's the entire payload:

| Field | Example | What it is |
|---|---|---|
| `installId` | `9f3c1d7a-…` | A random UUID generated on your machine the first time Buoy runs |
| `version` | `7.0.22` | The `@buoy-gg/core` version you have installed |
| `platform` | `ios` | `ios`, `android`, `web`, `macos`, or `windows` |
| `tier` | `free` | `anonymous`, `free`, or `pro` |

## What it never sends

- **Nothing from your tools.** Network requests, console output, storage values,
  Redux/Zustand/Jotai state, performance reports, screenshots — none of it. That
  data never leaves your machine, which is the entire reason Buoy is safe to run
  against production APIs and inside enterprises.
- **Nothing that identifies you.** No email, no license key, no account id.
- **Nothing that identifies your work.** No app name, bundle id, repo, package
  names, route names, or environment variables.
- **No device fingerprint.** `installId` is `Math.random()`-derived, not built
  from hardware or account properties. It cannot be correlated with anything
  else we store, and reinstalling produces a brand-new one.

## When it fires

- **Development only.** It is compiled out of release builds entirely. Your
  users' devices never contact us — a shipped app phoning home about a tool
  *you* chose to install would not be a defensible trade.
- **At most once every 24 hours**, per install.
- **Never blocking.** It's fire-and-forget: if you're offline, behind a proxy,
  or the endpoint is down, Buoy carries on without a warning in your console.

The first time it runs on a new install, Buoy prints one line to your console
saying it's on and how to disable it. Once — not on every launch.

## Why it exists

Buoy's licensing data can tell us how many people have a **free** or **pro**
key. It structurally cannot tell us how many people are running Buoy without
one, because there's no record of an install that never asked for anything.

That missing number is the denominator for the only question that matters when
deciding what to build next: of everyone who tries Buoy, how many find it worth
keeping? Without it we're guessing from npm download counts, which are heavily
inflated by CI.

## Blocked networks

The ping goes to `https://buoy.gg/api/t`. If your network blocks it, Buoy is
unaffected — the request fails silently and everything works normally.

If you need to allowlist Buoy's traffic in a corporate environment, the domain
that actually matters is **`api.keygen.sh`** (license validation). `buoy.gg` is
optional; blocking it costs you nothing.
