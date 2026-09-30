---
title: Release Builds
seoTitle: "Keep Buoy out of iOS Release builds"
id: swift-release-builds
description: "How Buoy for Swift stays inactive in Release, TestFlight and App Store builds, what it adds to your app size, and which features are limited to development builds."
---

Your app decides whether Buoy runs, through the guards you put around `Buoy.start` and the overlay.

## Guard both calls

Put `Buoy.start` and `.buoyDevTools()` (or `Buoy.install(in:)`) behind `#if DEBUG` or your own internal-build flag. An environment or user-role badge is presentation, not access control; apply your app's own authorization checks for users who can inspect or change its data.

TestFlight builds use the Release configuration, so a `DEBUG` guard normally excludes Buoy there. If you want Buoy in an internal TestFlight build, make that opt-in deliberate.

## What ships in Release

Swift packages cannot be linked only in Debug, so the Buoy framework is embedded in every build configuration. Nothing runs until `Buoy.start` is called: no URLProtocol registration, no swizzling, no overlay window and no network connection. With both calls guarded, a Release build contains the framework but never executes it.

Buoy 0.1.0 adds about 9 MB to an uncompressed arm64 Release build, measured before App Store thinning and compression.

## Development-only features

Two features also check the build at runtime, even if Buoy is started:

- Running scenarios is disabled in production builds.
- UI recording (Scenarios' **Record a flow**) needs a development build.

Buoy treats the simulator and apps signed with a development provisioning profile as development builds. Ad hoc, enterprise, TestFlight and App Store builds are not.

## Privacy manifest

The framework includes `PrivacyInfo.xcprivacy` with the app-scoped UserDefaults reason `CA92.1`. Buoy reads the host app's defaults and stores its own tool preferences there. Review your app's privacy disclosures for the diagnostic data you allow Buoy to capture and send to your broker.
