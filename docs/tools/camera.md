---
title: Camera
seoTitle: "iOS Simulator Camera — use your Mac camera, images and video in the Simulator"
id: tools-camera
description: "Give the iOS Simulator a working camera. Point it at your Mac screen, your webcam, an image or a video — scan QR codes and driver's licences, test capture flows, and stop reaching for a real device."
---

<!-- ::platform-badge platform="ios" -->

Buoy Desktop tricks the iOS Simulator into thinking it has a camera.

<!-- ::camera-demo -->

## Use it

Buoy Desktop → **Camera** → click a source. Then start your app however
you like. Sources: **Mac camera**, **Screen region**, **Barcode**, **Image**
(PNG, JPG, HEIC, WebP, GIF, BMP, TIFF), **Video** (MP4, MOV, M4V) and
**Test pattern**.

Nothing goes into your app, so this works on any booted simulator app — including
ones that have never heard of Buoy.

The camera attaches at process start, so relaunch anything already running — Fast
Refresh won't do it. Switching source afterwards is live. Permission is
pre-granted, so your app never prompts.

## Screen region

A resizable box on your desktop is the camera. Put it over a QR code, a licence
or a document and your app scans what's inside it.

Drag to move, pull a handle to resize, **Esc** to put it away. Resizing snaps to
**16:9** — the only shape the camera has, so anything else gets black bars; hold
**Shift** to override. **Click-through** lets clicks land underneath, so you can
scroll the page you're filming.

Needs Screen Recording permission — without it the feed is black. This source
needs [Buoy Pro](https://buoy.gg/pricing).

## Barcodes

QR, PDF417, Aztec, DataMatrix, EAN-8/13, UPC-E, Code 128/39/93, ITF.
`onBarcodeScanned` fires as normal. The **Barcode** source generates one from
text you type.

Generating QR is free. The rest — PDF417 (driving licences), Aztec, DataMatrix,
Code 128 — need [Buoy Pro](https://buoy.gg/pricing).

## Compatibility

Verified: `expo-camera`, `react-native-vision-camera` 5, Flutter's `camera`
plugin (preview, frame stream, photo and video recording), plain
`AVCaptureSession`. It's fabricated at the AVFoundation level, so UIKit/SwiftUI
should work too — untested.

## From an agent

Buoy's MCP server drives the whole tool, so a coding agent can run a camera test
end to end without a human: find a simulator and its apps (`camera_devices`) and
the Mac's cameras and windows (`camera_inputs`), pick what to show
(`camera_source`), attach it — to one app (`camera_launch`) or to everything the
simulator launches (`camera_zero_setup`) — then check its own work
(`camera_status`, `camera_diagnose`) and clean up (`camera_stop`).

No device connection and no Buoy integration needed. The MCP tools need
[Buoy Pro](https://buoy.gg/pricing); `camera_diagnose` works either way and will
tell you which tier you are on.

## Limits

- macOS + Xcode + Buoy Desktop. Screen sources need macOS 12.3+.
- Simulator only, not devices. Android emulators do webcams natively.
- No recording (`AVCaptureMovieFileOutput`) — preview and stills only.
- Front and back are the same feed.
- No GS1 DataBar or Micro symbologies.
- One injection owner at a time — quit other simulator-camera tools.
