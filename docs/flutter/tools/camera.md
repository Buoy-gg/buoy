---
title: Camera
seoTitle: "Flutter iOS Simulator Camera — use your Mac camera, images and video in the Simulator"
id: flutter-tools-camera
description: "Give the iOS Simulator a working camera for your Flutter app. Point it at your Mac screen, your webcam, an image or a video — scan QR codes and driver's licences, test capture flows, and stop reaching for a real device."
---

Buoy Desktop tricks the iOS Simulator into thinking it has a camera, so
Flutter's `camera` plugin finds one and your capture and scanning screens run
where the rest of your app already runs.

**iOS Simulator only.** Android emulators can already use a webcam natively, and
this is not a physical-device tool.

<!-- ::camera-demo -->

## Use it

Buoy Desktop → **Camera** → click a source. Then run your app however you like —
`flutter run`, from your IDE, or a build someone handed you. Sources: **Mac
camera**, **Screen region**, **Barcode**, **Image**, **Video**, **Test pattern**.

There is no package for this one. Nothing goes into your `pubspec.yaml`, nothing
wraps your widget tree, and `buoy_core` is not required — it works on any booted
simulator app, including apps that have never heard of Buoy.

The camera attaches at process start, so restart anything already running — hot
reload and hot restart won't do it, because the library is loaded by the process
before Dart runs. Switching source afterwards is live. Camera permission is
pre-granted, so your app never prompts.

## Screen region

A resizable box on your desktop is the camera. Put it over a QR code, a licence
or a document and your app scans what's inside it.

Drag to move, pull a handle to resize, **Esc** to put it away. Resizing snaps to
**16:9** — the only shape the camera has, so anything else gets black bars; hold
**Shift** to override. **Click-through** lets clicks land underneath, so you can
scroll the page you're filming.

Needs Screen Recording permission — without it the feed is black.

## Barcodes

QR, PDF417, Aztec, DataMatrix, EAN-8/13, UPC-E, Code 128/39/93, ITF.

Detection runs on your Mac, not in the simulator, and the results are handed to
the app through the same metadata API a real camera uses — so a scanner reading
`AVCaptureMetadataOutput` gets them without knowing anything happened. The
**Barcode** source generates a code from text you type, which is how you test a
licence scanner without owning a licence.

## What works

The devices are fabricated at the AVFoundation level, below anything Dart can
see, so what you build on top does not matter. Verified with Flutter's
[`camera`](https://pub.dev/packages/camera) plugin: preview, the frame stream,
photo capture, and video recording.

Plugins that go through `AVCaptureSession` — which on iOS is all of them —
should work the same way. That is an expectation, not a test result: only the
`camera` plugin has actually been run.

## Desktop & AI

This tool lives in [Buoy Desktop](../../desktop); there is no in-app half to it.
And with the [MCP server](../../mcp), an agent can run a camera test end to end
with no human in the loop: find a simulator and its apps (`camera_devices`), see
the Mac's cameras and windows (`camera_inputs`), pick what to show
(`camera_source`), attach it (`camera_launch`, or `camera_zero_setup` for
everything the simulator launches), then check its own work (`camera_status`,
`camera_diagnose`) and clean up (`camera_stop`).

No device connection and no Buoy packages needed for any of that.

## Limits

- macOS + Xcode + Buoy Desktop. Screen sources need macOS 12.3+.
- Simulator only, not devices. Android emulators do webcams natively.
- No recording through `AVCaptureMovieFileOutput` — the `camera` plugin's own
  video recording works, but that specific output class does not.
- Front and back cameras are the same feed.
- No GS1 DataBar or Micro symbologies.
- One injection owner at a time — quit other simulator-camera tools.

---

## What's Next

- [Images](./images) — Every image load, with cache verdicts and an oversize audit
- [Network Monitor](./network) — The requests your capture screen makes afterwards
- [Buoy Desktop](../../desktop) — Where this tool lives

---

## FAQ

### Do I need a Buoy package for this?

No. It is the one Buoy tool with nothing to install in the app — no
`pubspec.yaml` entry, no `BuoyDevTools` wrapper, not even `buoy_core`. Buoy
Desktop loads a small library into the simulator at launch and publishes frames
to it, so any booted simulator app gets a camera.

### Why does my Flutter app still say there's no camera?

The library is inserted when the process starts, so an app that was already
running when you turned the camera on never got it. Restart the app — hot reload
and hot restart both keep the same process, so neither is enough.

### Can I scan a QR code without printing one?

Yes, two ways. The **Barcode** source generates one from text you type. The
**Screen region** source points the camera at a rectangle of your own desktop,
so you can scan a code on a web page, in a PDF, or in a design file.

### Does this work on Android emulators?

It is not needed there — Android emulators can use a webcam as the camera
natively. This tool exists because the iOS Simulator ships no camera at all.
