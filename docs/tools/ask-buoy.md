---
title: Ask Buoy
seoTitle: "React Native AI Devtool — let QA and support drive your app in plain English"
id: tools-ask-buoy
description: "An in-app AI chat that drives every Buoy tool. Your QA tester types \"make the double burger out of stock and put it in my cart\" and it happens — on your own model endpoint, with a visible changes bar and real undo."
---

<!-- ::platform-badge platform="both" -->

> **Beta · Buoy Pro.** The API is settled and it is device-verified end to end, but this is the newest thing Buoy does and the surface may still move — pin the version if that matters to you. Ask Buoy requires a [Pro licence](https://buoy.gg/pricing); free and anonymous users see the tool and an upgrade prompt in its place.

Your QA tester types *"make the menu request fail with a 500"* and it happens. No ticket, no dev, no knowing what a cart-item object looks like.

Behind the chat, an agent drives the same ~190 tool actions Buoy Desktop and the MCP server use — network overrides, storage writes, cache pokes, impersonation, navigation — and answers in plain English. **The model is yours:** Buoy never holds a key and never proxies a request.

<!-- ::ask-buoy-live-demo -->

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/ask-buoy" yarn="yarn add @buoy-gg/ask-buoy" pnpm="pnpm add @buoy-gg/ask-buoy" bun="bun add @buoy-gg/ask-buoy" -->

This package is the chat. The *hands* are whichever Buoy tools you already have — each one you install becomes something the agent can do.

**Already running Buoy?** Upgrade every `@buoy-gg/*` package to the same version in the same command. Buoy pins `@buoy-gg/license` as an *exact* peer, so installing Ask Buoy on its own next to an older Buoy leaves an invalid dependency tree — and `npm install` exits 0 without saying so. `npm ls @buoy-gg/license` is what tells you.

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",   // or "openai" — covers Azure, Gemini-compat, most gateways
    model: "claude-opus-5",

    // Called before every request, so short-lived tokens work.
    // This is the whole auth story: your credential, your gateway.
    headers: async () => ({ Authorization: `Bearer ${await auth.getToken()}` }),
  }}
/>
```

The tool appears in the dial as **ASK BUOY**.

---

## Point it at a model

The `endpoint` is the one thing Buoy can't invent for you.

**Trying it out?** Talk to a provider directly with `apiKey`. It is compiled into your bundle in plaintext, so this is for your own simulator and nothing else.

**Shipping it to testers?** Put a ten-line proxy in front, so the key lives on a server and your app's own session is what authorises the call:

```js
export default {
  async fetch(request, env) {
    // Your gate: Ask Buoy sends whatever `headers` returned, so verify the
    // same session token the rest of your API already trusts.
    const upstream = await fetch("https://api.anthropic.com/v1/messages", {
      method: "POST",
      headers: {
        "content-type": "application/json",
        "x-api-key": env.ANTHROPIC_API_KEY,
        "anthropic-version": "2023-06-01",
      },
      body: request.body,
    });
    return new Response(upstream.body, { status: upstream.status });
  },
};
```

**Already have an AI gateway?** Point `endpoint` at it. Anthropic-shaped is the default; set `protocol: "openai"` for OpenAI-shaped ones. Bedrock and Vertex need SigV4/OAuth signing, which a static header can't express — front those with a proxy like the one above.

**Raise `maxTokens` on a thinking model.** Claude models reason by default, and that reasoning comes out of the same `maxTokens` budget as the answer — at the 4096 default a real multi-step turn gets cut off mid-sentence. 8192 is a sensible floor.

**Use a frontier model.** In our own 20-turn evals a budget model *changed the app to answer a question* — faking an API response, inventing a user — in 4 of 20 turns. A frontier model did it zero times. The guardrails below bound the damage; model quality is the first line.

### Teach it your data

Buoy learns your routes, query keys, store names and storage keys by watching the app run. What it can't guess is what they *mean*:

```tsx
context: {
  notes: [
    "user.rewards.points = loyalty points",
    "Prices are integer cents everywhere. `/api/*` ids are prefixed — ord_, usr_, itm_.",
  ],

  // Exact shapes for anything the agent may WRITE. Paste your real types —
  // nothing parses them, they go to the model as written.
  types: {
    CartLine: "{ lineId: string; itemId: string; qty: number; unitPrice: number }",
    Offer: "{ code: string; status: 'active' | 'expired'; endsAt: string /* ISO */ }",
  },
}
```

Twenty minutes of this, once, is the highest-leverage thing you can give it.

**Why `types` is separate from `notes`.** Ask Buoy prefers reading a live value over anything you declare — runtime truth can't go stale, and the prompt tells it to copy the field names it finds. But *there is nothing to read when the collection is empty*, which is exactly the moment someone asks for the first cart line. The runtime digest can't fill that gap either: it carries key and route **names** only, never values or shapes. So a note saying `item id = 123` gives the model the id and nothing about the object it goes in — it will invent `quantity` where you have `qty`, and your app renders nothing.

Declare a shape and that stops. Read wins over declared when both exist, and the agent flags it if they disagree; when neither exists it now says which shape it's unsure of instead of guessing silently.

**Don't write them by hand.** Everything these notes describe is already in your repo. Paste this into Claude Code, Cursor, Codex — any coding agent that can read the project — and have it do the first pass:

```text
Read this repo and fill in `context.notes` and `context.types` for Buoy's Ask
Buoy — an in-app AI agent that reads and writes this app's live state at
runtime. It already discovers route paths, query keys, and store and storage
key NAMES on its own, and it reads live values before writing. Your job is
what those names MEAN, and the SHAPES it cannot read when a collection is
empty.

Look at: route definitions, React Query key factories, Zustand/Redux/Jotai
store shapes, AsyncStorage/MMKV key constants, and the TypeScript types behind
anything a user sees.

`types` is a map of name -> shape text, for every object the agent might
CREATE or WRITE: cart lines, addresses, user records, feature flags, anything
a store or query cache holds a list of. Copy the real TypeScript type. Keep
optional markers and literal unions; strip imports, generics and methods.
Add a trailing `/* comment */` for a unit or format that the type alone does
not convey (cents, ISO date, id prefix). Nothing parses these — they are
handed to the model as written.

`notes` is one short plain-English fact per string, in this order:

1. What an ambiguous key, field, or store name MEANS in product terms.
2. Units and formats that apply broadly — cents vs dollars, ISO vs epoch ms,
   id prefixes.
3. Enum and status values, quoted exactly as the code spells them, where they
   are not already visible in a type above.
4. Which source is authoritative when two of them hold the same thing.
5. Any rule a newcomer gets wrong — a field that looks writable but is
   derived, two stores that must stay in sync.

Rules:
- Skip anything obvious from the name alone. `user.email` needs no note.
- No secrets, tokens, internal endpoints, or real customer data. These strings
  go to our model endpoint with every message.
- 20-30 notes and the shapes that actually get written. Dense beats
  exhaustive — every one of these is sent with every message.

Output only the `context` object, ready to paste.
```

Read what it gives you before shipping it — it is a first draft of the one input that most determines whether the agent gets your data right.

---

## What your team can do with it

| Who | What they type |
|---|---|
| **QA** | *"Make the next checkout call fail with a 500"* · *"Give me 1500 points"* |
| **Support** | *"Show me what user 8823 sees on the rewards screen"* · *"Is this a bug or did their offer expire?"* |
| **Product** | *"Put the app in demo state — gold tier, three items, promo applied"* |
| **Design** | *"Show me this card with a 60-character name, and again with no image"* |

---

## Nothing it changes is hidden

Reads and ordinary writes run immediately — that's what makes it fast enough to be worth using. What keeps you in control is the bar at the top of the sheet, and that undo is real.

The count is honest in both directions: storage writes are reversible because Buoy reads the old value *before* it writes; a state write with no captured prior value is labelled **permanent** rather than folded into a number Undo can't deliver; and one-shot actions like navigation aren't counted as changes at all. Typing **"undo that"** works too — the agent has its own undo tool wired to the same ledger as the bar.

**Destructive actions wait for a tap.** Wipes and resets show an approval card describing the *effect* — "Clear all saved app data" — not the raw payload. Tune it either way:

```tsx
policy: {
  requireApproval: ["write", "destructive"],               // stricter: every change asks
  allow: [{ effect: "read" }, { toolId: "impersonate" }],  // a scoped support seat
  readOnly: true,                                          // look, never touch
}
```

**SecureStore values are off by default** — they're credentials, and they'd travel to your model endpoint. Key *names* are always readable; opt into values with `secureReads: true`.

It also gets out of your way: when it navigates or taps, the sheet drops to a strip for a couple of seconds so you see the app do it. Every finished answer carries a **Copy** button — and so does the header — which puts the whole conversation on the clipboard as plain text, ready for Slack or a ticket.

**See what it actually did.** The header's gear opens **Settings → Chat → Show agent thinking**. On, every answer grows a collapsed strip — `2 steps · 1 thought · 3.2s · 6210 tokens` — that opens into the model's reasoning and each step it ran, in the order they happened. Tap a step for what it **sent** and what it **returned**.

That last part is the one that finds bugs. A step whose status is `ok` and whose result is `{"ok": false, "error": "no such key"}` looks like a working step until you can see the payload.

**Copy** — the one button under every answer — takes the whole conversation as plain text, and includes the working while this is on:

```text
You: show my cart
  [thinking]
    I should read the bag store first.
  [1] zustand.getStoreState — Done
      {"storeName":"Poké Mart bag"}
Ask Buoy: Here are the items in your cart.
```

Off by default, because the answer is what the sheet is for.

Not every model reports reasoning. Claude thinks by default; most OpenAI models return none and the strip then shows the steps alone, which it says rather than looking empty.

**The conversation survives a reload.** Reload the app, restart it, or crash it, and the chat is there when you come back — and so is the agent's memory of it, so "undo that" still means something. A turn the app died in comes back marked interrupted rather than blank. This is what makes the tool bearable while you iterate, and it is the only thing that survives a crash. **New conversation** deletes it.

---

## Which build should QA run?

**For the full pitch — forcing server responses, driving the screen — QA should run a development or internal build.** Some actions only work when `__DEV__` is true, and a few would otherwise *report success and do nothing*. Ask Buoy refuses those before running them and says why, and the sheet's first screen tells you which kind of build you're on.

A release build still reads storage, network, state, routes, console and crashes, and still impersonates and navigates — which is the support persona's whole job.

> **Note:** Ask Buoy is Pro in every build. Separately, in a release build Buoy itself only renders for a licensed Pro user at all. See [Buoy Pro](https://buoy.gg/pricing).

---

## Security

- **No credential in the app.** `headers` is a callback you own — no key in the bundle, nothing on disk, nothing on Buoy's sync wire.
- **Nothing reaches Buoy.** Requests go from the device to *your* endpoint. Buoy runs no inference service and no proxy.
- **Its own traffic is invisible to it**, so it can never read back its own auth headers.
- **Credentials are stripped** from tool results by field name *and* by shape (bearer tokens, JWTs, key patterns) before anything is sent.
- **The saved conversation holds no tool results.** It survives a restart (see above) under a `@react_buoy` key, capped and scrubbed for credential shapes — but only what was *said*. The payloads the agent read (storage values, response bodies, user records) are never written; it comes back knowing what it did, not what it saw. Turn the whole thing off with `persistTranscript: false` if the agent works over regulated data, since anything on disk under a Buoy key is readable by the Storage tool and, through it, by the unauthenticated broker.

### What we send to the model

1. Your message, plus a system prompt with your app's name, your `context.notes`, and a **names-only** digest of the running app — route paths, query keys, store and storage key *names*. Never values.
2. The results of tool calls it makes — state, storage values, response bodies — after the redaction above.
3. Nothing in the background: the digest is read once when the chat opens, and requests happen only while a turn runs.

The agent reads live app data, and some of that comes from services an attacker may influence. Prompt injection against tool-using agents is a real, unsolved class of attack. Ask Buoy bounds the blast radius — visible banner, real undo, destructive actions behind approval, and a system prompt that treats app data as data — but use `readOnly` or a tighter `requireApproval` against production data.

---

## Troubleshooting

- **A raw provider error on the first message** — the endpoint and `protocol` usually disagree. Ask Buoy warns when it can spot this itself.
- **"Couldn't reach your AI endpoint"** — the gateway dropped; the answer has a **Retry** button. Chronic cases are usually a corporate proxy buffering SSE.
- **It answers all at once instead of streaming** — React Native's built-in `fetch` has no streaming body. On Expo, `expo/fetch` as the global enables it.
- **An action is refused as "does not work in this build"** — that's the release-build truth doing its job, not a bug.

## Related

- [Scenarios](./scenarios) — once the chat gets a setup right, save it as a one-tap button for the team. Determinism beats a fresh LLM run every time.
- [Network overrides](./network) — the durable request faking Ask Buoy drives.
- [Impersonate](./impersonate) — see the app as a specific user.
- [Custom tools](../custom-tools) — register your own, and hand the agent their descriptors so it can drive them too.
