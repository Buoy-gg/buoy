# @buoy-gg/ask-buoy

**Ask Buoy** — an AI chat sheet inside your React Native app that drives every Buoy devtool, for the people on your team who don't read code.

Your QA tester types *"make the double burger out of stock at store 100 and put it in my cart"* and it happens. No ticket, no dev, no knowing what a cart-item object looks like. Behind the chat, an agent calls the same ~190 tool actions Buoy Desktop and the MCP server drive — network overrides, storage writes, react-query cache pokes, impersonation, navigation — and reports back in plain English.

**The model is yours.** Buoy never holds an API key and never proxies a request. You point Ask Buoy at your own endpoint and hand it headers from your app's own session.

## Setup

```bash
npm install @buoy-gg/ask-buoy
```

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",          // or "openai" — Azure, Gemini-compat, most gateways
    model: "claude-sonnet-4-5",

    // Called before every request, so short-lived tokens work.
    // This is the whole auth story: your credential, your gateway.
    headers: async () => ({
      Authorization: `Bearer ${await auth.getToken()}`,
    }),
  }}
/>
```

The tool appears in the dial as **ASK BUOY**. This package is the chat; the *hands* are the other Buoy tools you have installed — each tool package you add (network, storage, impersonate, …) becomes something the agent can do.

## What keeps it trustworthy

- **A visible changes bar** the moment it modifies anything — "Changed this app · 2 undoable · 1 permanent" — with real undo: storage writes are pre-read so Undo restores the exact prior value.
- **Destructive actions wait for a tap** by default; everything else auto-runs behind the bar. Tunable with `policy.requireApproval`, down to `readOnly: true` for a look-but-don't-touch support seat.
- **Release-build honesty**: actions that can't work outside a dev build are refused with an explanation, never silently "succeed".
- **Credential redaction** by field name and value shape before anything reaches your model; the agent's own traffic is invisible to it; the transcript is never persisted.

## Docs

Full guide — gateway templates, model guidance, the exact list of what's sent to the model, policy reference: **[buoy.gg/docs/tools/copilot](https://www.buoy.gg/docs/tools/copilot)**

The headless engine (catalog, providers, turn loop, effect ledger) lives in [`@buoy-gg/agent-core`](https://www.npmjs.com/package/@buoy-gg/agent-core).

## License

See [buoy.gg](https://www.buoy.gg) for licensing.
