---
title: Debug Borders
seoTitle: "Debug Borders for .NET MAUI"
id: maui-tools-debug-borders
description: "Draw a box around each view in your MAUI app. Tap a tag to see its id, size and style."
---

See how your screen is laid out.
Debug Borders draws a box around each view.
Tap a tag to learn about that view.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyDebugBorders();
```

The **BORDERS** button lives in the bubble's row.
Turn it on in Buoy's settings, under the floating tools.

## Three modes

Each tap of **BORDERS** moves to the next mode.

| Mode        | What you see                                      |
| ----------- | ------------------------------------------------- |
| **Off**     | No boxes.                                         |
| **Borders** | A box on each view. Its color shows how deep it is. |
| **Labels**  | Boxes and tags, but only on views with an id.     |

The icon is gray when off.
It turns green for Borders and blue for Labels.
Labels needs Buoy Pro.

Taps go right through the boxes to your app.
The boxes update every 2 seconds.
They hide while the dial or a tool is open.

## Labels

A view gets a tag if it has an `AutomationId` or a `SemanticProperties.Description`.

- A green tag shows the `AutomationId`.
- A pink tag shows the description.
- Tags sit just above their box.
- Tags that would bump into each other move up.

## Tap a tag

A sheet opens with facts about that view:

- **Identifiers**: its `AutomationId` and description.
- **Component**: its type, its parent's type and its native view.
- **Position & Size**: where it is, how big it is and how deep.
- **Accessibility**: its role, hint and state, if it has them.
- **Styles**: its size, see-through level, color and border.
