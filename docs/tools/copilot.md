---
title: Ask Buoy
description: An in-app AI chat that drives every Buoy tool — for the people on your team who don't read code.
---

# Ask Buoy

Your QA tester types *"make the double burger out of stock at store 100 and put it in my cart"* and it happens. No ticket, no dev, no knowing what a cart-item object looks like.

Ask Buoy is a chat sheet inside your app. Behind it, an agent calls the same ~185 tool actions that Buoy Desktop and the MCP server drive — network overrides, storage writes, react-query cache pokes, impersonation, navigation — and reports back in plain English.

**The model is yours.** Buoy never holds an API key and never proxies a request. You point Ask Buoy at your own endpoint and hand it headers from your app's own session.

```bash
npm install @buoy-gg/copilot
```

## Setup

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  copilot={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",          // or "openai" — also Azure and most gateways
    model: "claude-sonnet-4-5",

    // Called before every request, so short-lived tokens work.
    // This is the whole auth story: your credential, your gateway.
    headers: async () => ({
      Authorization: `Bearer ${await auth.getToken()}`,
    }),
  }}
/>
```

That's it. The tool appears in the dial as **ASK BUOY**.

### Teach it your data (20 minutes, once)

Buoy already learns your app by watching it run — routes, query keys, store names, storage keys, API hosts are all derived automatically with no setup. What it can't guess is what any of it *means*:

```tsx
copilot={{
  // …
  context: {
    notes: [
      "user.rewards.points = loyalty points",
      "['menu', storeId] = store inventory",
      "checkout.pendingOrder is cleared on a successful order",
    ],
  },
}}
```

This is the single highest-leverage thing you can give it. Three lines of plain English turn "I don't know where points live" into "done."

## What your team can do with it

| Who | What they type |
|---|---|
| **QA** | *"Make the next checkout call fail with a 500"* · *"Give me 1500 points"* · *"What did the last few network calls return?"* |
| **Support** | *"Show me what user 8823 sees on the rewards screen"* · *"Is this a bug or did their offer expire?"* |
| **Product** | *"Put the app in demo state — gold tier, three items, promo applied"* |
| **Design** | *"Show me this card with a 60-character name, and again with no image"* |

## Anything it changes, you can take back

Ask Buoy acts immediately rather than asking permission first — that's what makes it fast enough to be worth using. What keeps you in control is that **nothing it changes is hidden**.

A bar appears at the top of the sheet the moment it changes anything:

> ⚠️ **Ask Buoy changed this app — 2 undoable · 1 permanent**

Tap it to see exactly what changed and undo it. Storage writes are genuinely reversible: Ask Buoy reads the old value *before* it writes, so Undo restores the exact previous value (or removes the key if it didn't exist).

The count is honest. One-shot actions that have no inverse — clearing a cache, reloading — are listed as **permanent** rather than folded into a number that Undo silently can't deliver on.

### Want it to ask first?

```tsx
copilot={{
  // …
  policy: { requireApproval: ["destructive"] },   // or ["write", "destructive"]
}}
```

Approval cards describe the *effect* — "Give this user 1500 points" — not the raw tool payload, because a prompt nobody can read is a rubber stamp, not oversight.

Read-only mode, for a support seat that should look but never touch:

```tsx
policy: { readOnly: true }
```

## Release builds

Some Buoy actions only work in development builds, and this matters more than it sounds: a few of them **report success and do nothing** once `__DEV__` is false. A network override rule saves and persists, but the engine never consults it. Anything that walks the React tree (tapping an element, describing the screen) comes back empty, because React Native only installs the developer hook in dev builds — no configuration brings it back.

Ask Buoy **refuses those actions before running them** in a release build and tells the user why, rather than letting the agent report a change that never happened. A tester filing a bug against fake data that was never applied is worse than no agent at all.

Everything else — reading storage, network, state, routes, console; writing storage; impersonation; navigation — works normally.

> **Note:** in a release build Buoy itself only renders for a licensed Pro user. See [Licensing](/docs/licensing).

## Security

- **No credential in the app.** `headers` is a callback you own. There is no key in the bundle, nothing written to disk, nothing on Buoy's sync wire.
- **Nothing reaches Buoy.** Requests go from the device to *your* endpoint. Buoy operates no inference service and no proxy.
- **Its own traffic is invisible to it.** Requests to your endpoint are excluded from what the agent can read back through the network tool — otherwise it would read its own auth headers.
- **Credentials are stripped** from tool results before they're sent to your model, and from the transcript before it crosses to Buoy Desktop.
- **The transcript is never persisted.** It lives for the session.

### A limit worth stating plainly

The agent reads live app data — network response bodies, stored values, user records — and some of that data comes from services an attacker may be able to influence. Prompt injection against tool-using agents is a real, unsolved class of attack, and no filtering makes it go away.

What Ask Buoy does about it is bound the blast radius: a visible banner, a real undo, refusal of actions that can't work, and an opt-in approval gate. Use `readOnly` or `requireApproval` for anything running against production data.

## From the desktop

Buoy Desktop and the MCP server can see the conversation and clean up after it:

- `listChanges` — what the agent changed, and whether it's reversible
- `undoAll` — put back everything that can be put back
- `reset` — clear the transcript

There is deliberately **no** remote way to send a message. The broker has no authentication, so anyone on your network could otherwise drive a mutating agent on a tester's device. Conversations start on the device, with a person.

## Related

- [Scenarios](/docs/tools/scenarios) — once the chat gets a setup right, save it as a one-tap button for the rest of the team. Determinism beats a fresh LLM run every time.
- [Network overrides](/docs/tools/network) — the durable request faking Ask Buoy drives.
- [Impersonate](/docs/tools/impersonate) — see the app as a specific user.
