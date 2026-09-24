---
title: Ask Buoy
seoTitle: "React Native AI Devtool — let QA and support drive your app in plain English"
id: tools-ask-buoy
description: "An in-app AI chat that uses installed Buoy tools. Your QA tester types \"make the double burger out of stock and put it in my cart\" and it happens — on your own model endpoint, with visible action results and undo for supported changes."
---

<!-- ::platform-badge platform="both" -->

Ask Buoy is an in-app assistant for installed Buoy tools. It can inspect app data and run supported actions, such as editing storage or creating a development-only network override. It requires Pro and a model endpoint you configure. The feature is in beta.

Start with read-only access and a test build. Ask it to inspect a request, then check the tool result before enabling writes.

<!-- ::ask-buoy-live-demo -->

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/ask-buoy" yarn="yarn add @buoy-gg/ask-buoy" pnpm="pnpm add @buoy-gg/ask-buoy" bun="bun add @buoy-gg/ask-buoy" -->

Install the tool packages for the actions you want Ask Buoy to use, and complete their setup first.

Keep Buoy packages on compatible versions, including their exact license peer requirement. Check your dependency tree with `npm ls @buoy-gg/license` after upgrading; a successful install command alone does not establish compatibility.

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",   // or "openai" — covers Azure, Gemini-compat, most gateways
    model: "YOUR_MODEL_ID",
    policy: { readOnly: true },

    // Called before every request, so short-lived tokens work.
    // Your gateway must validate the token and authorize the request.
    headers: async () => ({ Authorization: `Bearer ${await auth.getToken()}` }),
  }}
/>
```

The tool appears in the dial as **ASK BUOY**.

---

## Point it at a model

Use an authenticated gateway that supports the selected protocol. `headers` supplies your app's session token on each request; the gateway must validate it, authorize the user, constrain model access and request size, and preserve streaming responses. Keep the provider credential on the server.

The installation example assumes your app defines `auth.getToken()`. Replace the endpoint and model ID with values your gateway supports. Do not expose a gateway that forwards requests without validating the caller.

For a private local experiment, `apiKey` can call a provider directly, but that key is included in the app bundle. Do not distribute that build to testers.

`protocol: "anthropic"` uses an Anthropic-shaped API; `"openai"` uses the corresponding compatible API shape. Services requiring request signing need a gateway that performs that signing. Choose token limits and provider options for the model you actually use, then test tool calls, streaming, errors, and interrupted turns.

Before enabling writes, send a harmless request such as “List the installed tools.” Check that the endpoint accepts the token, the action list is correct, and no app state changes.

### Teach it your data

Buoy learns your routes, query keys, store names and storage keys by watching the app run — and, for Zustand stores, the field names of the items in every list they currently hold. What it can't guess is what any of that *means*:

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

Review these definitions when your app schema changes. They guide the model but do not replace validation in the tools.

**Why `types` is separate from `notes`.** Ask Buoy prefers an observed shape over anything you declare — runtime truth can't go stale, and the prompt tells it to copy the field names it finds. The digest gives it a head start where it can: for every list a **Zustand** store currently holds, it carries that list's item field names and types, so `lines[]` arrives as `{ lineId: string; itemId: string; qty: number; … }` before the agent reads anything. Values never travel — only names, types and the sketch of a shape. Redux slices and Jotai atoms are reported by name alone, so if that is where your data lives, `types` is the only way the agent learns its shape without reading an instance first.

But *there is nothing to observe when the collection is empty*, which is exactly the moment someone asks for the first cart line. The digest is honest about that rather than papering over it: an empty list is reported as a list whose shape is **unknown**. A note saying `item id = 123` doesn't fill the gap either — it gives the model the id and nothing about the object it goes in, so it invents `quantity` where you have `qty`, and your app renders nothing.

Provide types for objects the agent may create, especially empty collections. Ask Buoy is instructed to prefer observed shapes and flag disagreements; test the resulting writes rather than assuming a declaration guarantees correctness.

**Don't write them by hand.** Everything these notes describe is already in your repo. Paste this into Claude Code, Cursor, Codex — any coding agent that can read the project — and have it do the first pass:

```text
Read this repo and fill in `context.notes` and `context.types` for Buoy's Ask
Buoy — an in-app AI agent that reads and writes this app's live state at
runtime. It already discovers route paths, query keys, store and storage key
NAMES, plus the item field names of lists held in Zustand stores, and it reads
live values before writing. Your job is what those names MEAN, and the SHAPES
it cannot observe — anything in Redux or Jotai, and any collection that is
currently empty.

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

1. For each main screen: what it shows and WHERE that data comes from — the
   React Query key pattern and endpoint, or the store and slice. This is how
   the agent knows that "change the name on the screen I'm looking at" means
   the query cache on one screen and a store on another.
2. Which screen loads the FULL set of something, and which screens load only
   part of it. Ask Buoy can only read what your app has already fetched, so
   when someone asks for "all the X" it has to know where all of them are —
   "the full list of items is on /catalog; the home carousel fetches one card
   at a time as you swipe". Without this it can still go looking, but with it
   it goes straight there.
3. What an ambiguous key, field, or store name MEANS in product terms.
4. Units and formats that apply broadly — cents vs dollars, ISO vs epoch ms,
   id prefixes.
5. Enum and status values, quoted exactly as the code spells them, where they
   are not already visible in a type above.
6. Which source is authoritative when two of them hold the same thing.
7. Any rule a newcomer gets wrong — a field that looks writable but is
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

### Write down your team's procedures

Some tasks aren't guessable from the app: *"reproduce the pricing glitch"*, *"put this account in the expired state"*, *"set up the demo cart"*. Write them once as **procedures** and the agent follows them when someone asks:

```tsx
context: {
  procedures: [{
    id: "expired-subscription",
    title: "Put the account into the expired-subscription state",
    summary: "when asked to test what an expired or lapsed subscriber sees",
    body: `1. Read the zustand store "account" and note subscription.status.
2. setState { subscription: { status: "expired", renewsAt: null } } — a merge, not a replace.
3. Navigate to /account. Done when the renew banner is showing.
Undo puts the real status back.`,
    requires: ["zustand", "route-events"],   // only listed when these tools are installed
  }],
}
```

Only the `id` and one-line `summary` ride in every prompt; the body loads when a request matches, so a long playbook costs nothing until it's needed. A tester then types **"run the expired-subscription check"** and the agent opens the procedure first and follows it — instead of asking what that means. A procedure guides; it grants nothing: every step still goes through the same policy, approval card, undo and checks as any other call.

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

The default policy allows reads and ordinary writes immediately and asks for destructive actions. Use read-only mode for initial setup, or require approval for every change as shown below. The changes bar records supported reversible changes and identifies changes it cannot undo.

The count is honest in both directions: storage writes and query-cache edits are reversible because Buoy reads the old value *before* it writes (a cache edit the app has since refetched is left alone, and Undo says so); a state write with no captured prior value is labelled **permanent** rather than folded into a number Undo can't deliver; and one-shot actions like navigation aren't counted as changes at all. Typing **"undo that"** works too — the agent has its own undo tool wired to the same ledger as the bar.

**Destructive actions wait for a tap.** Wipes and resets show an approval card describing the *effect* — "Clear all saved app data" — not the raw payload. Tune it either way:

```tsx
<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.example.com/v1/messages",
    model: "YOUR_MODEL_ID",
    headers: async () => ({ Authorization: `Bearer ${await auth.getToken()}` }),
    policy: {
      requireApproval: ["write", "destructive"],
      maxSteps: 12,
    },
  }}
/>
```

`policy.readOnly: true` refuses writes. For scoped access, set `allow` rules for permitted effects or tools; nonmatching calls are refused. `deny` rules take precedence. Keep these configurations separate from the approval example above.

The card takes a **note** — type *"just the second line"* before **Not now** and the agent gets your words verbatim, instead of a round trip of "what would you prefer?". **Allow for this chat** approves *and* stops asking about that action for the rest of the conversation — ten storage writes are one tap, not ten. It waives the approval card only: read-only, deny lists and release-build refusals still hold. **Settings → Permissions** lists what's been waived, each with an **Ask again** button, and a new conversation forgets all of it.

The card's long description is collapsed behind **Details**, so the buttons are always reachable — and a card you never answered comes back after a reload, still answerable. Tapping **Allow** then runs the change itself, through the same gate and the same undo ledger.

**It checks its own work.** "The tool said ok" is not the same as "the app shows it", so after a write the agent reads the app back. The activity row says **Verified** when the requested state is really there; **Done · not yet visible** when the write landed but the outcome hasn't shown yet — an override installed that nothing has fetched through — and the agent is told what would make it show (a refresh, a visit to that screen) before it may claim anything; or **Done · check failed** when the app doesn't hold what was written, in which case it may correct *once*, with a different change, and never repeats the write or reports it as done. Tap the row for the reason. Storage writes, store and cache edits, navigation and override rules are checked today.

**Or look without touching.** On a shared or support device, **Settings → Permissions → Read only** refuses every write and simulation until you turn it off — the header says **Read only** while it's on. It adds to whatever the app's `policy` already restricts and never loosens it, it takes effect on the very next call even mid-turn, and Undo still works, because putting things back is the safe direction.

**Or turn the asking off entirely.** On your own dev device, the header's gear opens **Settings → Permissions → Skip approval prompts**: every action then runs the moment the agent calls it, wipes included. It's the "I'm moving fast" switch — it waives the *approval* gates only, so `readOnly`, `deny` and the release-build refusals still hold, and the changes bar still records everything with Undo. It persists across reloads and restarts until you turn it off, and the settings row stays amber while it's on so you can see that it is.

**SecureStore values are off by default** — they're credentials, and they'd travel to your model endpoint. Key *names* are always readable; opt into values with `policy.secureReads: true`.

**You can keep typing while it works.** Anything you send mid-turn is parked in a list above the composer — in order, tap a row to edit it, ✕ to drop it — and sent one at a time as each turn finishes. The list holds, and says why, after an error, after Stop, and while a question or approval is waiting for you: a parked message is a next task, never an answer. **Stop** stops before the next call; anything already in flight finishes and keeps its receipt, every call it skipped shows as "Not run — stopped", and the composer invites you to say what to do instead. **Try again** keeps the stopped attempt above the new one.

**A question it asks you is always tappable.** When the agent needs a decision — your request could mean two things, or it's offering to do something you didn't quite ask for — the answer comes back as buttons, not as a sentence expecting you to type "yes". If it ever asks in prose anyway, Buoy turns that into a card for it. Typing over the card still works; it just stops being the only way.

It also gets out of your way: when it navigates or taps, the sheet drops to a strip for a couple of seconds so you see the app do it. Minimize it and it keeps going — if it then needs a tap, the approval waits and its chip in the minimized dock gets a **!** badge; tap the chip and the card is there. (Closing the sheet still stops the turn.)

**Big results aren't lost.** A tool result over 24,000 characters is cut for the model — but the whole thing is kept for the conversation, and the agent can read any part of it back by field, by search term or by window. So *"what did the server send for the third item?"* is answerable even when that field sits far past the cut, and *"what did that look like before you changed it?"* comes from what the agent actually saw at the time, not a fresh read of the new value. The kept copy lives in memory for the conversation only and is never written to disk.

**Reading a turn.** Consecutive reads fold into one row — *Looked at Network and Storage · 3 reads* — and every write, failure, refusal and declined call stays its own row. A quiet line under each answer says how long it took and how many actions ran. Long tables say when they're cut and offer **Show all**; code stays as code.

Every finished answer carries **Copy conversation** — and so does the header — which puts the whole conversation, cards included, on the clipboard as plain text. For a bug report, the header's document button asks the agent to **summarize for a ticket**: one card with steps, expected, observed, build, the evidence it actually saw and what's still applied, with anything it inferred kept in its own row — and its own **Copy finding**. Nothing is run or changed to produce it.

**See what it actually did.** The header's gear opens **Settings → Chat → Show agent thinking**. On, every answer grows a collapsed strip — `2 steps · 1 thought · 3.2s · 6210 tokens` — that opens into the model's reasoning and each step it ran, in the order they happened. Tap a step for what it **sent** and what it **returned**.

That last part is the one that finds bugs. A step whose status is `ok` and whose result is `{"ok": false, "error": "no such key"}` looks like a working step until you can see the payload.

**Copy conversation** — the button under every answer — takes the whole conversation as plain text, and includes the working while this is on:

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

**The conversation survives a reload.** Reload the app, restart it, or crash it, and the chat is there when you come back — and so is the agent's memory of it, so "undo that" still means something. A turn the app died in comes back marked interrupted rather than blank, messages you had parked come back as unsent rows, and an approval you never answered comes back as a card that says it predates the restart — **Allow** re-reads what it would change and refuses if that has moved on since. In a very long session the agent's memory is trimmed before the screen is; a quiet line marks the gap when that happens — and the exchanges whose changes are still applied (the override you armed, the user you're impersonating) are the *last* to go, so "turn that off" keeps meaning something. This is what makes the tool bearable while you iterate, and it is the only thing that survives a crash. **New conversation** deletes it.

---

## Watch it from your desk

If the device is also connected to [Buoy Desktop](../desktop), Ask Buoy shows up there too — as a **read-only mirror**: the conversation as it streams, what the agent has changed and whether each change can be put back, and what the turn has spent in tokens. It's how you follow a tester's session from your own machine without standing over their shoulder.

Desktop exposes two actions: **Undo everything reversible**, and **reset** (which undoes first and refuses to clear if a revert fails, rather than dropping the only record of what is still applied).

Desktop has no chat composer. Start conversations on the device. Use the broker only on a trusted development network; account admission is not per-user authorization to control another device.

---

## Which build should QA run?

**Use a development build for response overrides and screen-driving actions.** An internal distribution label alone does not make `__DEV__` true. Some actions only work when `__DEV__` is true, and a few would otherwise *report success and do nothing*. Ask Buoy refuses those before running them and says why, and the sheet's first screen tells you which kind of build you're on.

A release build still reads storage, network, state, routes, console and crashes, and still impersonates and navigates — which is the support persona's whole job.

> **Note:** Ask Buoy is Pro in every build. Separately, in a release build Buoy itself only renders for a licensed Pro user at all. See [Buoy Pro](https://buoy.gg/pricing).

---

## Configuration reference

Everything the `askBuoy` prop takes.

| Option | Default | What it does |
|---|---|---|
| `endpoint` | *required* | Where the conversation is POSTed. The only thing Buoy can't invent for you. |
| `model` | *required* | Model id, passed through to the endpoint. |
| `protocol` | `"anthropic"` | The wire shape the endpoint speaks — `"anthropic"` or `"openai"`. |
| `headers` | — | `() => Record<string,string>` (may be async), resolved **before every request** so short-lived tokens work. This is the intended auth story. |
| `apiKey` | — | A provider key sent straight to the provider. **Dev only** — it is compiled into your bundle in plaintext. |
| `maxTokens` | `4096` | Ceiling for one response. Choose a value supported by your model and large enough for its response and reasoning requirements. |
| `anthropicVersion` | — | The `anthropic-version` header. Ignored when `protocol` is `"openai"`. |
| `requestOverrides` | — | Extra fields merged into every request body, last — `temperature`, a gateway's routing hints, whatever your endpoint demands that this config doesn't model. |
| `policy` | destructive asks | What the agent may do without asking. See [above](#nothing-it-changes-is-hidden). |
| `context` | — | `{ notes, types, procedures }` — what your data *means*, the shapes of what it may write, and your team's playbooks. Keep these aligned with your app schema. |
| `appName` | the app name | Shown in the sheet header and given to the model. |
| `peek` | `true` | When the agent navigates or taps, the sheet drops to a strip for a couple of seconds so the user sees the app do it. Set `false` to keep the sheet fixed. |
| `persistTranscript` | `true` | Keep the conversation across a reload, restart or crash. `false` keeps it in memory for the session and no longer. |
| `onEvent` | — | Every engine event as it happens — tool starts and results, outcome checks (`tool-verified`), blocks, approvals, retries, errors, per-request token usage with prompt-cache counters and the model id the provider actually served, and a `stopReason` on every `done`. Log agent activity to your own systems, meter cost per seat. Called synchronously on the JS thread: keep it cheap. Throws are swallowed so a logging bug can't take the chat down. |
| `tools` | — | Descriptors for **your** custom tools, so the agent can drive them too. See below. |

### Let it drive your own tools

A [custom tool](../custom-tools) with a `sync` adapter is already dispatchable as `custom:<id>` — Buoy Desktop and MCP can drive it today. Ask Buoy is different: it only offers what's in its catalog, so without a descriptor your tool is invisible to the chat while staying fully drivable from MCP.

```tsx
tools: [{
  toolId: "custom:feature-flags",   // must match the registered id
  title: "Feature flags",
  summary: "Read and set this app's feature flags.",
  actions: [{
    action: "setFlag",
    summary: "Turn one feature flag on or off.",
    params: {
      type: "object",
      properties: { name: { type: "string" }, on: { type: "boolean" } },
      required: ["name", "on"],
    },
    effect: "write",     // read | write | destructive — drives the approval gate
    release: "works",    // works | noop | empty | throws | unknown — anything but "works" is refused in a release build
  }],
}]
```

Your actions then get the same param validation, policy gates and release-build refusals as the built-ins. Write a real `summary` and real param names: a parameter the model isn't shown the name of is a parameter it will guess at.

---

## Security

- **Gateway credentials.** With `headers`, your app obtains a session token and sends it to your endpoint. Keep provider keys on the gateway. Direct `apiKey` configuration embeds a provider credential in the app.
- **Model traffic.** Conversation requests go to your configured endpoint. Buoy account validation and telemetry are separate; see [Telemetry](../telemetry).
- **Its own traffic is invisible to it**, so it can never read back its own auth headers.
- **Credentials are stripped** from tool results by field name *and* by shape (bearer tokens, JWTs, key patterns) before anything is sent.
- **The saved conversation holds no tool results.** It survives a restart (see above) under a `@react_buoy` key, capped and scrubbed for credential shapes — but only what was *said*. The payloads the agent read (storage values, response bodies, user records) are never written; it comes back knowing what it did, not what it saw. (The full copies it can re-read mid-conversation are held in memory, after credential redaction, and vanish with the conversation.) Turn the whole thing off with `persistTranscript: false` if the agent works over regulated data, since anything on disk under a Buoy key is readable by the Storage tool and, through it, by clients with access to your development broker. Account validation does not provide per-user device authorization.

### What we send to the model

1. Your message, plus a system prompt with your app's name, your `context.notes` and `context.types`, and a **values-free** digest of the running app: route paths, store and storage key *names*, the *field names and types* of the items in each Zustand store's lists (a shape sketch, bounded in depth, width and total size — never the records themselves), and a "right now" block re-read before each message with the current route, which query keys are mounted on screen, and the method + URL of the last few requests. Never values, never bodies.
2. The results of tool calls it makes — state, storage values, response bodies — after the redaction above.
3. Nothing in the background: the digest is read when the chat opens and the "right now" block when you send a message; requests happen only while a turn runs.

The agent reads live app data, and some of that comes from services an attacker may influence. Prompt injection against tool-using agents is a real, unsolved class of attack. Ask Buoy limits available actions — visible banner, real undo, destructive actions behind approval, and a system prompt that treats app data as data — but use `readOnly` or a tighter `requireApproval` against production data.

---

## Troubleshooting

- **A raw provider error on the first message** — the endpoint and `protocol` usually disagree. Ask Buoy warns when it can spot this itself.
- **"Endpoint busy — retrying in 4s"** — a rate limit or an overloaded provider. The agent waits and asks again by itself (up to twice, and only when nothing was generated yet, so nothing ever runs twice); you don't need to do anything.
- **"Couldn't reach your AI endpoint"** — the gateway dropped; the answer has a **Retry** button. Chronic cases are usually a corporate proxy buffering SSE.
- **"This conversation is too large for the AI endpoint"** — the model's window is full and there was nothing older to trim. Start a new conversation, or ask a shorter question. (The agent trims older exchanges by itself first, and measures the endpoint's real token count as it goes, so this is rare on a 128k+ window.)
- **It answers all at once instead of streaming** — React Native's built-in `fetch` has no streaming body. On Expo, `expo/fetch` as the global enables it.
- **An action is refused as "does not work in this build"** — that's the release-build truth doing its job, not a bug.

## Related

- [Scenarios](./scenarios) — once the chat gets a setup right, save it as a one-tap button for the team. Determinism beats a fresh LLM run every time.
- [Network overrides](./network) — the durable request faking Ask Buoy drives.
- [Impersonate](./impersonate) — see the app as a specific user.
- [Custom tools](../custom-tools) — register your own, and hand the agent their descriptors so it can drive them too.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
