# @buoy-gg/agent-core

The headless engine used by Ask Buoy. It has no React, React Native, or Node built-in imports.

It provides:

- **The tool catalog** — Buoy tool actions, generated from one source file. JSON-Schema params validated *before* dispatch, per-action effect classes (read / write / destructive), and per-action release-build truth: actions requiring development-only capabilities are refused with an explanation, never allowed to fake success. Parameter schemas describe the names and values accepted by each action.
- **The turn loop** — provider streaming (Anthropic and OpenAI wire shapes, with prompt caching where the provider offers it), tool dispatch with independent reads overlapped, result redaction (credential field names *and* value shapes), self-traffic stripping, loop detection, history compression before rounds are dropped, and step and time caps — with a `stopReason` on every `done`, the rounds holding a still-applied change dropped from memory last, and a throttled or oversized request retried narrowly: only when the stream produced nothing, so nothing runs twice.
- **The evidence store** — every result kept in full after redaction, so `ask-buoy.retrieve` can read any part of a result cut at 24k, or re-read exactly what the model saw before a write. Session-scoped, in memory, never persisted.
- **Outcome checks** — five writes are read back after they land (storage, store and cache edits, navigation, override rules); the verdict rides in the tool result and on a `tool-verified` event: `verified` / `unverified` (landed, not yet visible — and what would make it so) / `failed` (one correction, never a repeat).
- **Token calibration** — budgets stated in tokens and converted at a ratio measured from the provider's own usage reports, not a guessed 4 chars/token.
- **The grounding digest** — a compact, values-free picture of the running app built from the tools themselves: route paths, storage and store key names, mounted-first query keys, and the item **shape** of every non-empty list in a store, with empty lists named as unknown rather than left to a guess. Bounded in depth, width and total characters, so a store keyed by a few hundred records can't turn a "compact" sketch into the largest thing in the prompt.
- **The effect ledger** — what the agent changed and how to put it back. Storage writes and query-cache edits are pre-read so undo restores exact prior values; whatever can't be undone is reported as permanent rather than hidden.
- **Policy** — approval gates (destructive waits for a human tap by default), allow/deny rules, per-call approval, read-only mode, and a SecureStore-values gate that is off by default. `requestApproval` may resolve `{ approved, reason, trust }`: a note with a decline reaches the model verbatim, and `trust` waives the card for that action for the session — approval only, never past policy.
- **Procedures** — `context.procedures` are the app's playbooks; only `id — summary` is in the prompt, the body is served by the engine-served `ask-buoy.openProcedure`.
- **Blocks** — the closed, declarative UI catalog (`buoy_ui`) the model authors rich responses with, plus engine-made receipts projected from tool results.

A host supplies the model endpoint, protocol, model, available actions, and a `dispatch(toolId, action, params)` function. The host must also enforce account access, authenticate model requests, and handle permissions and lifecycle events.

## Use it

For the React Native chat interface, use [`@buoy-gg/ask-buoy`](https://www.npmjs.com/package/@buoy-gg/ask-buoy), the React Native chat sheet built on this. It installs this engine as a dependency.

Direct use is for custom hosts. This is an integration sketch: `endpoint`, `model`, `myDispatch`, and `availableActions` come from your host; it is not a standalone app.

```ts
import { createAskBuoySession } from "@buoy-gg/agent-core";

const session = createAskBuoySession({
  endpoint, protocol: "anthropic", model,
  dispatch: myDispatch,           // (toolId, action, params) => Promise<unknown>
  availableActions,               // what this host can actually reach
  isRelease: false,
});

for await (const event of session.send("what did the last network call return?")) {
  // text deltas, reasoning, tool starts/ends, blocks, approvals, token usage
}
```

The built-in catalog validates supported actions and their parameters. Expose only actions your host can dispatch, and consult the Ask Buoy configuration guide for policy and custom-tool integration.

## Docs

**[buoy.gg/docs/tools/ask-buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy)**

## License

See [buoy.gg](https://buoy.gg) for licensing.
