---
title: "Ask Buoy: teach it your app"
seoTitle: "Ask Buoy setup — notes, types, team steps and every option"
id: tools-ask-buoy-teach-it-your-app
description: "Tell Ask Buoy what your app's data means, the shapes it may write, and the steps your team repeats. Plus every askBuoy option and how to let it drive your own tools."
---

Start with the [Ask Buoy](../ask-buoy) page. This page is for when it works and you want better answers.

## Teach it your data

Buoy learns names by watching your app run. It sees routes, query keys, store names and storage keys. For Zustand stores, it also sees the field names of items in each list. What it can't guess is what those names *mean*. Tell it with `context`:

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

**Why `types` matters.** Ask Buoy trusts what it sees in the app over what you write. But an empty list has nothing to see. Say someone asks for the first cart line. Without a type, the AI guesses the fields. It might write `quantity` when your app wants `qty`, and your screen shows nothing.

Redux slices and Jotai atoms only send their names. If your data lives there, `types` is how Ask Buoy learns its shape. Add a type for each object it may create, most of all for lists that can be empty.

Update these when your app changes. They guide the AI, but they don't check anything. Test the changes it makes.

### Let your coding agent write them

All of this is already in your code. Paste this into Claude Code, Cursor, Codex or any coding agent that can read your project:

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

Read what it gives you before you ship it. It's a first draft.

## Write down your team's steps

Some jobs can't be guessed from the app. Think *"put this account in the expired state"* or *"set up the demo cart"*. Write them once as **procedures**:

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

Only the `id` and `summary` go with each message. The `body` loads when a request matches it. So a long procedure costs nothing until someone needs it. A tester types *"run the expired-subscription check"*, and Ask Buoy follows the steps.

A procedure gives no extra power. Each step still goes through the same rules, approvals and Undo as any other action.

## Let it drive your own tools

A [custom tool](../../custom-tools) with a `sync` adapter can already be driven by Buoy Desktop and MCP as `custom:<id>`. Ask Buoy only sees tools in its list, though. Give it a descriptor:

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

Your actions then get the same checks, rules and release-build refusals as Buoy's own. Write a real `summary` and clear param names. The AI guesses at anything it can't see.

## Rules for what it may do

The default lets it read and make normal changes right away. Big changes wait for a tap. You can change that:

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

- `readOnly: true` refuses every change.
- `allow` lists the tools or kinds of change it may use. Anything else is refused.
- `deny` wins over `allow`.
- `secureReads: true` lets it read SecureStore values. They are off by default because they are secrets.

The same `policy` works with `hostedAskBuoy({ policy })`.

## Configuration reference

Everything the `askBuoy` prop takes. `hostedAskBuoy()` fills in `endpoint`, `protocol`, `model`, `headers` and `maxTokens` (8192) for you. You can pass it the rest, except `apiKey`, `anthropicVersion` and `requestOverrides`.

| Option | Default | What it does |
|---|---|---|
| `endpoint` | needed for your own model | Where each message is sent. |
| `model` | needed for your own model | Model id, sent to your endpoint as is. |
| `protocol` | `"anthropic"` | How your endpoint talks: `"anthropic"` or `"openai"`. |
| `headers` | — | `() => Record<string,string>`, may be async. Runs **before every request**, so short-lived tokens work. Use this for sign-in. |
| `apiKey` | — | An AI key sent straight to the AI company. **Only on your own machine.** It ends up in your app in plain text. |
| `maxTokens` | `4096` | Most tokens in one answer. Pick what your model supports. |
| `anthropicVersion` | — | The `anthropic-version` header. Not used with `"openai"`. |
| `requestOverrides` | — | Extra fields added to every request body last, like `temperature`. |
| `policy` | big changes ask | What it may do without asking. See [above](#rules-for-what-it-may-do). |
| `context` | — | `{ notes, types, procedures }`. See [above](#teach-it-your-data). |
| `appName` | the app name | Shown in the header and told to the AI. |
| `peek` | `true` | When it taps or changes screens, the sheet shrinks for a moment so you see the app. `false` keeps it still. |
| `persistTranscript` | `true` | Keep the chat after a reload, restart or crash. `false` keeps it in memory only. |
| `onEvent` | — | Gets every event: tool calls, results, checks, approvals, retries, errors and token use per request. Use it to log activity or track cost. It runs on the JS thread, so keep it fast. Errors in it are ignored. |
| `tools` | — | Descriptors for your own tools. See [above](#let-it-drive-your-own-tools). |
