---
title: Scenarios
seoTitle: "React Native Test States — one-tap app states for QA"
id: tools-scenarios
description: "Save the app state you just set up as a button anyone can tap. Override an API response, write storage, impersonate a user and navigate — in one step, with an unmissable SIMULATED banner and one-tap undo."
---

<!-- ::platform-badge platform="both" -->

Getting an app into a hard state is the tax on testing it. Out of stock. Expired offer, mid-checkout. Payment declined on the second card. Gold tier, ten points from the next tier. Every one of those is a Slack message to a developer, a shared test account somebody else already dirtied, or a race against a five-minute expiry window — and the states that need two people and a phone call simply don't get tested.

A **scenario** is that setup, saved as a button. Pick the variables, tap Run, and the running app is in the state you asked for: the request returns a 500, the storage key is written, you're impersonating the right user, and you're on the right screen. While it's active an unmissable amber banner says so, and one tap puts reality back.

## Installation

<!-- ::PM npm="npm install @buoy-gg/scenarios" yarn="yarn add @buoy-gg/scenarios" pnpm="pnpm add @buoy-gg/scenarios" bun="bun add @buoy-gg/scenarios" -->

Auto-discovery finds the installed package and the SCENARIOS tool appears in your floating menu:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

A scenario's steps are executed through the tools that already own that state, so install the ones your scenarios use — `@buoy-gg/network` for response overrides, `@buoy-gg/storage` for storage writes, `@buoy-gg/impersonate` for user sessions, `@buoy-gg/route-events` to navigate. A step whose tool isn't installed is refused *before* the run starts, and the preview tells you which one.

---

## Ship the QA menu with your app

The authoring path that lasts is code. `defineScenario()` gives you a typed, reviewable, versioned definition that lives next to the code it bends:

```tsx
// scenarios/qaMenu.ts
import { defineScenario } from "@buoy-gg/scenarios";

export const outOfStock = defineScenario({
  id: "oos-item",
  name: "Out-of-stock item at store {{storeId}}",
  description: "The double burger is sold out, one is already in the cart.",
  expectedOutcome:
    "Cart shows the item as sold out; checkout is blocked until it's removed.",
  author: "@sam",
  authoredAgainst: "5.4.0",
  folder: "Checkout",        // the flow it belongs to — see Folders below
  tags: ["cart", "menu"],
  vars: [
    { key: "storeId", label: "Store", type: "enum",
      options: ["100", "220", "415"], default: "100" },
  ],
  steps: [
    { tool: "network", action: "upsertOverrideRule",
      params: { rule: {
        urlPattern: "*/menu/{{storeId}}*", methods: ["GET"],
        kind: "respond", status: 200, body: '{"items":[{"id":"dbl","inStock":false}]}',
      } } },
    { tool: "storage", action: "async.setItem",
      params: { key: "@cart", value: '[{"item":"dbl","qty":1}]' } },
    { tool: "route-events", action: "navigate", params: { path: "/cart" } },
  ],
});
```

Register them and they show up under **Shipped with this app**, read-only on the device:

```tsx
import { createScenariosTool } from "@buoy-gg/scenarios";
import { outOfStock, expiredOffer } from "./scenarios/qaMenu";

const scenarios = createScenariosTool({
  scenarios: [outOfStock, expiredOffer],
  currentBuild: "5.4.0",   // drives the staleness badge
});

<FloatingDevTools apps={[scenarios]} />
```

Now QA's menu is a build artifact that ships, gets reviewed in pull requests, and changes with the API it fakes — instead of living in one developer's fingers.

---

## What You Can Do

- **Run a saved state in one tap** — pick the variables (real pickers, never free text for a URL), tap Run, and the app bends. On success the tool minimizes itself: the changed app is the confirmation.
- **See exactly what will happen before it happens** — the preview lists every step in plain English (`GET */menu/100* → respond 200, replaced body`, `Set storage @cart`, `Go to /cart`) with a durability chip on each, plus the author's expected outcome. You can write a bug report against a scenario because you can read what it did.
- **Trust the boundary between fake and real** — while a scenario is active, an amber `▲ SIMULATED` banner shows on every screen, names the scenario, counts its changes, and carries the deactivate control. It survives app restarts alongside the overrides, and because it's a real view it lands in every screenshot and screen recording you take. There is no setting that hides it.
- **Deactivate, and be told the truth** — one tap deletes the scenario's override rules and ends its impersonation, then reports what it could *not* reverse. Storage writes have no automatic inverse, so they're named rather than quietly left behind. Authors can supply `undoSteps` to close that gap.
- **Survive a restart** — network overrides ride the shipped override-rule store, so the state holds through pull-to-refresh, a JS reload, and a full app relaunch. The active record is reconciled against the real rules on boot, so the banner can never claim state that isn't there.
- **Never stack two scenarios by accident** — activation is exclusive. Running a scenario that touches an endpoint the active one already owns offers a **Swap** instead of layering, and the overlap is named. Two scenarios silently fighting over the same request is a lost afternoon, not a feature.
- **Author from a conversation** — set the state up interactively with your AI assistant, then ask it to save that as a scenario. It arrives on the device as a **draft** (see below).
- **Attach the state to the bug** — **Copy repro info** puts the scenario name, version, variables, build and effect list on the clipboard, formatted for a ticket. "Are you sure a scenario wasn't active?" stops being a question anyone has to ask.

---

## Folders: one flow at a time

A QA library grows past the point where one list is useful. Give a scenario a **folder** — the flow it belongs to — and the tool grows a chip bar across the top: `All · Checkout · Login · Onboarding · Ungrouped`. Tap one and the list is only that flow. Somebody testing checkout stops scrolling past login.

Three ways a scenario gets filed:

- **In code**, with `folder:` in `defineScenario()`. The QA menu ships organized, and it looks the same on every device — which is the point. Shipped scenarios are read-only on the device, folder included.
- **On the device**, from a scenario's detail screen: tap the **Folder** row, pick an existing folder or type a new one.
- **From your editor**, with `scenario_action` `setFolder`, or a `folder` on the scenario you save.

A folder is a label on a scenario, not a record somewhere. That has consequences worth knowing:

- **It exists as long as something is in it.** Move the last scenario out and the folder is gone. There is nothing to clean up, and no empty folders to wonder about.
- **Names converge instead of splitting.** Typing `checkout` when `Checkout` already exists files it under `Checkout`. Names are trimmed, inner whitespace is collapsed, and they cap at 32 characters.
- **One folder per scenario.** That's what makes it a folder rather than a tag — use `tags` for the many-to-many axis.
- **There is no rename or delete.** A rename would silently skip the read-only code scenarios sharing the name, leaving half a folder behind. Change the `folder:` in the source, or re-file the device ones.

Search reads the folder too, so typing `checkout` finds the flow before you've noticed the chips.

---

## Drafts: nothing writes to a device without a human

A scenario saved from chat or the desktop dashboard does **not** become runnable. It lands in a **Drafts — awaiting review** section and is inert: pre-flight refuses to run it. Someone on the device opens it, reads the plain-English steps, and taps **Accept to library**.

This is deliberate. A tester's device state is their chain of custody — if state can change under them mid-session, every surprising behavior becomes a suspect, including the real bugs. Review costs five seconds; not having it costs the tool's credibility.

---

## Durability, honestly

Different steps last for different lengths of time, and the UI never softens the difference.

| Step | Durable? | What deactivate does |
|---|---|---|
| Override a request | ✅ survives refetch, JS reload and app restart | Deletes the rule |
| Impersonate a user | ✅ survives restart | Ends the session |
| Set / remove storage | ✅ it's the app's real store | **Not auto-reversed** — the key is named, or `undoSteps` restore it |
| Poke the query cache | ❌ dies on the next refetch | Nothing to remove; labelled transient throughout |
| Navigate | one-shot | n/a |

The durable path is the recommended path. `setQueryData` stays available because it's the right tool for a one-shot demo state, but it is always labelled *until next refetch* — a scenario that quietly expires is worse than one that never ran.

---

## Variables

Declare them, and the run form builds itself: an `enum` renders as chips, a `boolean` as a switch, a `number` as a numeric field. Values are remembered per scenario, so a regression pass doesn't retype them.

Placeholders substitute by `{{key}}` and nothing else — no expressions, no templating language. A scenario is data, never code, which is what makes it safe to commit, to share, and to accept from an AI. One rule worth knowing: a string that is *exactly* `"{{code}}"` keeps the variable's type, so `status: "{{code}}"` sends the number `500`, not the string `"500"`; embedded placeholders substitute as text.

---

## Failure is loud

Pre-flight runs before step one and collects **every** problem at once — an uninstalled tool, an action that doesn't exist, an undeclared variable, a plan limit already reached. You never bend half the world and then discover a typo in step four.

If a step still fails at runtime, the run stops there, prints the error verbatim, marks the rest skipped, and makes you choose: **Undo applied steps** (reversing exactly what landed) or **Keep**. There is no silent default, and a partially applied scenario is never recorded as active.

---

## Drive it from your editor and your tests

Every action is available over MCP and from Buoy Desktop, so the same named state serves QA's finger, an E2E spec and an AI agent:

| Action | What it does |
|---|---|
| `listScenarios` | Every scenario with its plain-English step lines |
| `preview` | Dry-run: what a run *would* do, with variables resolved |
| `run` | Runs it; resolves once every step has been applied |
| `getActive` | The active scenario — assert it at the top of a spec |
| `save` | Saves a scenario onto the device **as a draft** |
| `setFolder` | Files a saved scenario under a flow, or clears its folder |
| `deactivate` | Reverses it and returns the honest report |

Because `run` only resolves once the state is really applied, it works as a deterministic fixture: run the scenario, then drive the flow. One arrange layer, shared by the person tapping, the test suite and the agent.

> **Not to be confused with** WireMock's "scenarios" (a stateful mock state-machine) or a Gherkin scenario (a test case). A Buoy scenario is closer to the *Given* half of a Gherkin scenario, made executable.

---

## Safety

- **Development builds only.** The runner refuses to execute when `__DEV__` is false, with a release-lane test proving it. Scenario definitions in a shipped app are inert data.
- **The banner is not optional.** It can be collapsed to a small amber pill; it cannot be turned off while a scenario is active.
- **A scenario can't run a scenario.** Recursion is refused in pre-flight.
- **Your own traffic is protected.** Override steps flow through the shipped engine, so its exclusions (license checks, the broker, Metro) and its `OPTIONS` skip apply automatically.
- **Size budgets** — 64KB per step, 256KB per scenario, validated on every save. Prefer the smallest response body that triggers the state.

## Plans

| | Free | Pro |
|---|---|---|
| Scenarios saved on a device | 3 | Unlimited |
| Active at once | 1 | 3 |

Scenarios shipped in code with `defineScenario()` don't count toward the device limit.
