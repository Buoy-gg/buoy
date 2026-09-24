# @buoy-gg/ask-buoy

Ask Buoy adds an AI chat to your React Native app. It can inspect and act through installed Buoy tools, subject to your policy and each tool’s integration.

For example, a tester can ask it to simulate a failed checkout request when Network overrides are configured. It reports the actions it took and exposes available undo operations. Some changes cannot be undone.

You configure the model endpoint and authentication. Requests go from the app to that endpoint. Use an authenticated gateway that validates the caller and keeps provider secrets on the server.

## Setup

```bash
npm install @buoy-gg/core @buoy-gg/ask-buoy
```

## Account and configuration

Ask Buoy requires Pro access. Use a development build with `@buoy-gg/core` and configure your Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

This configuration fragment uses your existing `auth.getToken()` implementation. Your gateway must validate that token. Replace the endpoint and model with values your gateway supports.

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",          // or "openai" — Azure, Gemini-compat, most gateways
    model: "YOUR_MODEL_ID",

    // Called before every request, so short-lived tokens work.
    headers: async () => ({
      Authorization: `Bearer ${await auth.getToken()}`,
    }),
  }}
/>
```

Open Ask Buoy and ask it to read a known captured request. Confirm that it reports the actual request before trying a write. Available actions depend on installed tools and policy. Start with `policy: { readOnly: true }` in the `askBuoy` configuration if you want to validate reads first.

Keep package versions compatible with their declared peer dependencies, especially `@buoy-gg/license`. Check package-manager peer warnings when upgrading.

### Teach it your data

Buoy discovers routes, query keys, store names and storage keys by watching the app run, and reads the item shape of every non-empty list it can see — what it can't know is what any of that *means*, or the shape of a collection that is currently empty.

Add `context` inside the `askBuoy` configuration. For example:

```tsx
const context = {
  notes: ["Prices are integer cents everywhere. `/api/*` ids are prefixed — ord_, usr_, itm_."],
  types: { CartLine: "{ lineId: string; itemId: string; qty: number; unitPrice: number }" },
};
```

The docs carry a prompt you can paste into a coding agent to draft both from your repo.

Tasks that aren't guessable from the app — *"reproduce the pricing glitch"*, *"put this account in the expired state"* — go in `context.procedures`: an `id`, a one-line `summary` (the only part in every prompt), and a `body` with the steps, loaded when a request matches. A tester types **"run the expired-subscription check"** and the agent opens the playbook first instead of asking what that means. A procedure guides; it grants nothing.

## Permissions and verification

The changes bar lists undoable and permanent changes. Supported storage and query-cache writes capture the prior value for undo. Changes without a captured prior value are marked permanent.

Destructive actions require approval by default. Configure `policy` to require more approvals, allow or deny actions, limit steps, or enable read-only access. An approval note is passed to the model. **Allow for this chat** waives approval for that action during the conversation; it does not override read-only mode, deny rules, or release restrictions. Review or revoke these waivers in Settings.

Supported writes have outcome checks. The activity row reports **Verified**, **Done · not yet visible**, or **Done · check failed**. Inspect the affected screen or state when a result is unverified. Actions requiring development hooks are refused in release builds.

Credential redaction uses field names and value patterns. SecureStore values require `policy.secureReads`. Review the data your tools expose; redaction cannot establish that arbitrary app data is safe to send to a model.

## Sessions and debugging

- Saved sessions can restore the transcript, agent memory, and pending approvals. Set `persistTranscript: false` to disable transcript persistence. Saved transcripts omit tool results.
- Large, redacted results remain in memory for the conversation. The agent can retrieve fields or sections omitted from the model’s initial result. This evidence store is not persisted across restarts.
- Eligible rate-limit and overload responses are retried when no output was generated. Oversized requests can trim history and retry. The sheet reports retry activity.
- Answers can include tables, choices, diffs, images, receipts, and approval cards. Messages sent during a turn queue for later processing.
- **Settings → Chat → Show agent thinking** exposes available reasoning and step payloads, results, timing, and token counts. Inspect the result as well as the step status when diagnosing a failed action.
- A ticket summary can collect steps, expected and observed behavior, build information, and available evidence without running new actions.

## Desktop and custom integrations

Desktop can mirror the conversation and expose supported remote undo. There is no remote composer. Mirroring requires external sync and account admission; your app still controls authorization.

Custom tools need a sync adapter and a `ToolDescriptor` passed through `tools`. They use the same parameter validation and policy checks as built-in actions.

Use `onEvent` to observe tool calls, outcome checks, approvals, retries, errors, and token usage. Each `done` event includes a `stopReason`.

## Docs

For gateway requirements, model configuration, data sent to the model, and policy options, see **[buoy.gg/docs/tools/ask-buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy)**

The headless engine (catalog, providers, turn loop, effect ledger, blocks) lives in [`@buoy-gg/agent-core`](https://www.npmjs.com/package/@buoy-gg/agent-core).

## License

Ask Buoy requires a [Buoy Pro licence](https://buoy.gg/pricing). See [buoy.gg](https://buoy.gg) for licensing.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
