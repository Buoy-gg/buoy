---
title: Focus Inspector
seoTitle: "React Native TV Focus Inspector — debug D-pad focus on Apple TV and Android TV"
id: tools-focus-inspector
description: "See what holds D-pad focus in a React Native TV app from Buoy Desktop: every focus move with its direction, the focusable inventory, and automatic flags for focus that gets stuck, vanishes, or is never reached."
---

<!-- ::platform-badge platform="both" -->

Inspect observed focus transitions, the current focus target, and the scanned focusable elements in a React Native TV app. Flags identify patterns worth testing, such as repeated unsuccessful moves or an element that has not been reached.

Treat flags as diagnostic evidence. Confirm a suspected bug with the same remote sequence and your intended focus behavior.

## Installation

Complete [TV Quick Start](../tv/quick-start) first, including core, `@buoy-gg/external-sync`, your account key, and a working Desktop connection. Then add this tool.

<!-- ::PM npm="npm install @buoy-gg/focus-inspector" yarn="yarn add @buoy-gg/focus-inspector" pnpm="pnpm add @buoy-gg/focus-inspector" bun="bun add @buoy-gg/focus-inspector" -->

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools headless licenseKey={process.env.EXPO_PUBLIC_BUOY_KEY} />  {/* TV apps run headless — no bubble */}
    </>
  );
}
```

Then open **Focus** in [Buoy Desktop](../desktop) and drive your app with the remote — or with
the [TV Remote](./tv-remote) tool, which presses it for you.

Requires `react-native-tvos` and the New Architecture. On a phone build the tool reports "not a
TV build" and attaches nothing.

---

## What you see

### Now

The element that currently holds focus: its `testID`, component name, native tag, how much of the
screen it occupies, and how long it has been focused.

Two warnings live here, and they are the ones worth knowing about:

- **Focus is parked on a `TVFocusGuideView`.** A guide with `destinations` is a genuine focus
  stop with no highlight. To whoever is holding the remote, focus just disappeared.
- **Focus was lost.** A view blurred and nothing took focus after it — focus left the React
  Native tree entirely (a system dialog, a native view, an unmounted subtree). This is reported
  only when the tool *watched it happen*; "we have not seen focus yet" is a separate, calmer
  message that never claims anything is wrong.

### Observed exits

A D-pad compass showing where each direction has been *seen* to lead from the focused element.

A direction not yet pressed from the current element remains unknown. Observed exits describe transitions that occurred; they do not predict untested paths through guides, overrides, or traps.

### Transitions

Every focus move, newest first: the direction credited to it, where focus came from, where it
went, and how long it had been sitting there. A move to `LOST` is focus leaving the tree.

### Focusables

Everything the focus engine could land on, in reading order — including elements below a
ScrollView's fold and the invisible guide stops. Each row shows whether focus has ever reached
it, the props that make it focusable (`hasTVPreferredFocus`, `nextFocus*`, guide settings), and a
**Focus this** button.

That button is the tool's most useful interaction: put focus somewhere, then press directions and
watch what actually happens from there. It is also the only thing this tool ever writes.

---

## The flags

| Flag | What it means |
|---|---|
| **Dead end** | You pressed one direction three or more times in a row from the same element and focus never moved — and there *is* something focusable further along that way. |
| **Focus trap** | A region focus was never observed escaping in any direction where something focusable exists outside it. Usually a `TVFocusGuideView` and everything inside it. |
| **Invisible stop** | Focus landed on a guide, or on an element the scan has never seen. This is what "the highlight vanished" looks like. |
| **Focus lost** | A view blurred and nothing took focus afterwards. |
| **Suspected unreachable** | Never focused, nothing observed pointing at it, and a visited element sits right next to it. |

Two things the flags will not do.

They will not call anything **unreachable**. Never having reached an element is evidence, not
proof — tvOS's focus engine is non-deterministic enough that certainty is not available without
native support that does not exist. So the strong word is reserved for nothing, and the panel
tells you when you have not traversed enough for absence to mean anything yet.

They will not blame the screen edge. If nothing focusable exists past an element in that
direction, focus refusing to move is the screen ending, not a bug — that gets counted and set
aside, not flagged.

Flags belong to the screen the current inventory describes. Navigate away and they reset with the
rescan, rather than turning every element of the old screen into a mystery.

---

## Platform differences worth knowing

`TVFocusGuideView` and `trapFocus*` are documented for both platforms in [react-native-tvos](https://github.com/react-native-tvos/react-native-tvos#tvfocusguideview). A trapped region may be intentional on Android TV as well as tvOS. Compare the flag with your props, framework version, and observed navigation before classifying it as a defect.

**Focus and blur arrive in different orders.** tvOS delivers the new element's focus first and
the old element's blur second; Android does the opposite. The tool handles both, to distinguish transitions from focus loss.

`Platform.isTVOS` is `undefined` on Android TV, not `false` — a detail that has cost more than
one team an afternoon.

---

## What it costs your app

Two event listeners, always, and the expensive part only on demand.

The focus listeners start with the app rather than when you open the panel, because focus cannot
be *asked about* on either platform — it can only be watched arriving. A tool that started
listening when you opened it would know nothing until focus next moved, and would be blind in the
exact situation you opened it for: focus is stuck, you press a direction, nothing moves, so
nothing is learned. What that costs is two event-emitter subscriptions and a bounded ring-buffer
append per focus change.

The fiber scan — the part with real cost — only runs while Buoy Desktop is actually watching. Use
**Pause recording** in the panel to stop the rest.

The tool renders **nothing** on the device, and never will. On Android TV any focusable view in
an overlay becomes a D-pad stop in your app's own focus order — a visible focus tool would change
the thing it is measuring. Buoy Desktop is the surface.

There is no native code in this package: no podspec, no Gradle, nothing to link. The inventory is
a read-only walk of the React tree, and the same key sequence produces the same focus path with
the tool running and with it removed.

---

## Pairs well with

- **[TV Remote](./tv-remote)** — press the D-pad from your desktop and record the sequence as a
  macro. Replay a macro while the Focus Inspector records, and you have a repeatable focus
  regression test.
- **[Routes](./routes)** — when focus "vanishes" on navigation, the route timeline usually
  says why.

## Web support (unreleased)

Browser capture measures DOM focusable elements and observes real focus and keyboard events. It uses the existing sync adapter for remote inspection. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
