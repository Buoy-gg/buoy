---
title: Focus Inspector
seoTitle: "React Native TV Focus Inspector — debug D-pad focus on Apple TV and Android TV"
id: tools-focus-inspector
description: "See what holds D-pad focus in a React Native TV app from Buoy Desktop: every focus move with its direction, the focusable inventory, and automatic flags for focus that gets stuck, vanishes, or is never reached."
---

<!-- ::platform-badge platform="both" -->

TV focus bugs are the ones you can only describe by waving your hands. "It gets stuck down
there." "Sometimes the highlight just disappears." "That button — you can see it, but you can't
get to it." Nothing is thrown, nothing is logged, and the only witness is whoever was holding the
remote.

The Focus Inspector turns that into a record. It watches every focus change your app makes,
credits each one to the direction that caused it, inventories everything the focus engine could
land on, and flags the three ways TV focus actually breaks: it gets **stuck**, it **vanishes**,
or an element is **never reached**.

## Installation

<!-- ::PM npm="npm install @buoy-gg/focus-inspector" yarn="yarn add @buoy-gg/focus-inspector" pnpm="pnpm add @buoy-gg/focus-inspector" bun="bun add @buoy-gg/focus-inspector" -->

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools headless />  {/* TV apps run headless — no bubble */}
    </>
  );
}
```

Then open **Focus** in [Buoy Desktop](../desktop) and drive your app with the remote — or with
the [TV Remote](tv-remote) tool, which presses it for you.

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

A direction you have not pressed from here reads **unknown** and stays dim. That is deliberate.
There is no API on either platform that answers "if focus is here and the user presses RIGHT,
where does it go" — Android has one natively but never exposes it to JavaScript, and tvOS's focus
engine decides at runtime and answers nothing at all. Every edge in this tool is one your app
actually took. A tool that guessed the rest would be wrong in precisely the cases you opened it
for: focus guides, `nextFocus*` overrides, and traps.

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

**`trapFocus*` holds on tvOS and silently does not hold on Android TV.** The same code traps
focus on one platform and does nothing on the other. So a trap flagged on Android is *always* a
real bug, whatever your props say — and a trap flagged on tvOS with `trapFocus` set is probably
what you asked for. The panel tells you which one you are looking at.

**Focus and blur arrive in different orders.** tvOS delivers the new element's focus first and
the old element's blur second; Android does the opposite. The tool handles both, which is why a
transition never disappears and a fast traversal never fabricates a "focus lost".

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

- **[TV Remote](tv-remote)** — press the D-pad from your desktop and record the sequence as a
  macro. Replay a macro while the Focus Inspector records, and you have a repeatable focus
  regression test.
- **[Routes](route-events)** — when focus "vanishes" on navigation, the route timeline usually
  says why.
