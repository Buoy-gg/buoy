---
title: Assets
seoTitle: "Assets for .NET MAUI"
id: maui-tools-assets
description: "See every image, font and file your MAUI app ships. Find big, unused and duplicate ones."
---

See every image, font and file your app ships.
Find the big ones, the copies and the ones you never use.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyAssets();
```

## Where the list comes from

When you build in Debug, Buoy makes a list.
It has each `MauiImage`, `MauiAsset` and `MauiFont` in your project.
For each one, it saves the size, a hash and the picture's size.

The list ships inside your Debug app.
A Release build does not get it.
To turn it off, set `BuoyAssetManifest` to `false`.

Files named `@2x` or `@3x` count as one asset.
Each one is a scale of the same picture.

## The list

Each asset gets one row.
It shows a small copy, its name and its size.
The biggest ones come first.
Tap **↓ SIZE** to sort by name or by type.

The chips at the top filter the list.
Pick images, fonts, copies or assets not seen yet.
Type in the search box to find one by name.

The header shows how many assets there are.
It also shows the total size and the fonts in use.

## Used or not

An image counts as used once your app shows it by name.
A font counts once you add it with `ConfigureFonts`.

**NOT OBSERVED** means Buoy did not see it in use yet.
It may still be used on a screen you did not open.
So try your app first, then look again.

## What it checks

- **OVERSIZE**: the picture is bigger than two screens.
- **DUP**: two files have the same bytes.
- **HEAVY**: a GIF over 100 KB, or a font over 150 KB.
- **BAD SCALE**: a sharper copy is smaller than a blurrier one.
- **LOW-RES**: no copy fits this phone's screen.
- **WEBP**: a PNG or JPEG over 50 KB that could shrink.

## The detail page

Tap a row to see one asset.
You see a preview, what it found and every size.
A font shows a line of sample text.
Use the arrows at the bottom to step through the list.

The copy button copies a report for that asset.
On the list, it copies the whole list.
The trash button clears the list until the next scan.

See [all MAUI tools](../tools).
