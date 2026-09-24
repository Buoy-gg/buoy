---
title: Camera
seoTitle: "iOS Simulator Camera — use your Mac camera, images and video in the Simulator"
id: tools-camera
description: "Supply camera feeds to supported iOS Simulator apps. Review account setup, source options, Pro requirements and camera-library compatibility."
---

<!-- ::platform-badge platform="ios" -->

Buoy Desktop supplies a camera feed to supported iOS Simulator apps on macOS. Sign in with a Buoy account before using the Desktop panel. Webcam, image, video, pattern and QR generation are available at Free limits; screen-region capture and non-QR generation require Pro access.

<!-- ::camera-demo -->

## Use it

Buoy Desktop → **Camera** → click a source. Then start your app however
you like. Sources: **Mac camera**, **Screen region**, **Barcode**, **Image**
(PNG, JPG, HEIC, WebP, GIF, BMP, TIFF), **Video** (MP4, MOV, M4V) and
**Test pattern**.

The app under test does not need the Buoy SDK. Its camera library and capture APIs must still support the simulated camera path.

The camera attaches at process start, so relaunch anything already running — Fast
Refresh won't do it. Switching source afterwards is live. Grant the host camera or screen-recording permissions required by your source, and verify the app's own capture flow.

## Screen region

A resizable box on your desktop is the camera. Put it over a QR code, a licence
or a document to supply frames to your app. Verify its scanner callback and payload handling separately.

Drag to move, pull a handle to resize, **Esc** to put it away. Resizing snaps to
**16:9** — the only shape the camera has, so anything else gets black bars; hold
**Shift** to override. **Click-through** lets clicks land underneath, so you can
scroll the page you're filming.

Needs Screen Recording permission — without it the feed is black. This source
needs [Buoy Pro](https://buoy.gg/pricing).

## Barcodes

QR, PDF417, Aztec, DataMatrix, EAN-8/13, UPC-E, Code 128/39/93, ITF.
Decoding support depends on the app scanner and capture path. The **Barcode** source generates supported formats from text you type; generation and decoding are separate checks.

Generating QR is free. The rest — PDF417 (driving licences), Aztec, DataMatrix,
Code 128 — need [Buoy Pro](https://buoy.gg/pricing).

## Compatibility

Camera libraries can disable Simulator paths at compile time. Check the version you use and test preview, scanning, still capture and recording separately. A working preview or a video used as input does not establish recording-output compatibility.

Use `buoycam patch` to inspect known library guards before applying a patch. A native-source patch requires rebuilding the app. See the [Camera setup page](https://buoy.gg/camera) for the host workflow and diagnostics. No compatibility table on this page substitutes for testing your app and library version.

## From an agent

Buoy's MCP server drives the whole tool, so a coding agent can run a camera test
through the supported MCP actions: find a simulator and its apps (`camera_devices`) and
the Mac's cameras and windows (`camera_inputs`), pick what to show
(`camera_source`), attach it — to one app (`camera_launch`) or to everything the
simulator launches (`camera_zero_setup`) — then check its own work
(`camera_status`, `camera_diagnose`) and clean up (`camera_stop`).

These host tools do not require an SDK connection from the app under test. MCP process/account setup still applies. The camera actions need
[Buoy Pro](https://buoy.gg/pricing); `camera_diagnose` works either way and will
tell you which tier you are on.

## Limits

- macOS + Xcode + Buoy Desktop. Screen sources need macOS 12.3+.
- Simulator only, not devices. Android emulators do webcams natively.
- No recording through `AVCaptureMovieFileOutput`. Recording through other paths, such as the Flutter camera plugin, has separate support.
- Front and back are the same feed.
- No GS1 DataBar or Micro symbologies.
- One injection owner at a time — quit other simulator-camera tools.
