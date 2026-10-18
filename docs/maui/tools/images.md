---
title: Images
seoTitle: "Images for .NET MAUI"
id: maui-tools-images
description: "See each image your MAUI app loads. Find big, slow and broken images."
---

See each image your app loads.
Find images that are too big, slow or broken.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyImages();
```

That is all.
Buoy watches each MAUI `Image` in your app.
It reads web, file and app images.

## The list

Each image gets one row.
A row shows a small copy of the image and its file name.
It also shows how long it took and its size in pixels.

A dot shows if it loaded, is still loading, or failed.
A red FAILED tag marks a broken image.

Buoy checks the size, too.
It compares the image's pixels to the box it shows in.
A big image in a small box says "oversized".
A small image in a big box says "upscaled".
That one looks blurry.

On iPhone, the phone may shrink a big image to fit its box.
Then Buoy shows that smaller size, just like on React Native.

The top line counts images and failed loads.
It also guesses how much memory they use.

Chips under the top line point out common problems:

- The same web address loaded twice
- An image that keeps trying again
- More than 4 images loading at once
- Images with no alt text
- Images whose box changed size after load

Alt text is the image's `SemanticProperties.Description` or `AutomationProperties.Name`.

## The detail

Tap a row to see more.
You see where the image came from and how it loaded.
You also see its pixel size and the box size.
If it failed, you see why.

Arrows step to the last or next image.
Reload loads it again.
Hard reload skips the cache.
You can also try a new web address on the same image.

## Make images fail on purpose

These buttons change the image on screen.
Your app sees the change too.

- **Error** makes the image fail.
- **Loading** makes it load forever.
- **Blank** hides it.
- **Find** puts a red border on it for 2.5 seconds.
- **Restore** puts the real image back.

The list has the same buttons for every image at once.
It also has three network modes:

- **Normal** loads images like always.
- **Offline** makes web images fail. App images still load.
- **Cold** skips the cache, like the first launch.

Tap Reload after you pick a mode.
Images on screen then load with it.

## What MAUI can't show

MAUI does not tell Buoy where an image came from in the cache.
So the cache tag shows "?".
Failed loads don't show a web status code.
The button that shrinks a big image is not on MAUI.

Buoy leaves its own images out of the list.

See [all MAUI tools](../tools).
