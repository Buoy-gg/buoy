---
title: Image Overlay
seoTitle: "Image Overlay for .NET MAUI"
id: maui-tools-image-overlay
description: "Lay a design picture over your MAUI app. Line it up and check your work."
---

Lay a design picture over your app.
Line it up with your screen.
Then fade it to spot what is off.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyImageOverlay();
```

## Tag the views to match

Give a view an `AutomationId` that starts with `image-target:`.
The words after it are the name you see in the list.

```xml
<Border AutomationId="image-target:LoginCard">
    <Label Text="Welcome back" />
</Border>
```

Buoy only looks at ids with that start.
Your other `AutomationId` values stay as they are.
It also skips views that are hidden.

## Pick a mode

**Component Match** finds each tagged view on the screen.
Tap one to pin the picture to it.
The picture is sized to fit the view's width.

Turn on **Auto Track** to follow the view.
Buoy checks where it is on each frame.
So the picture stays on it as you scroll.

**Free Placement** puts the picture in the middle of the screen.
Drag it to move it.
Drag a corner dot to make it bigger or smaller.
It keeps its shape.

## Load a picture

- **Paste from Clipboard** uses a picture you copied.
  If you copied a web address, it goes in the box.
- Or type a web address and tap **Load**.

## Tune it

- **Opacity** fades the picture in or out.
- **Scale** makes it bigger or smaller.
- **Offset X** and **Offset Y** nudge it a few points.
- **Flip** turns it left to right or top to bottom.
- **Show Outline** draws a dashed box on the view.
- **Lock Position** stops the picture from moving.
  Taps then go through to your app.
- **Reset Settings** puts the fit and fade back.
- **Remeasure** finds the view again.

The power button hides the picture.
The trash button clears it all.

Buoy's windows stay on top of the picture.
So you can always reach the menu.

See [all MAUI tools](../tools).
