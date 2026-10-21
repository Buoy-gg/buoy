---
title: Release Builds
seoTitle: "Keep Buoy out of Android release builds"
id: android-release-builds
description: "Keep Buoy out of the Android builds you ship with the no-op package, or ship it on purpose to internal testers with a Pro plan."
---

You decide which builds get Buoy.
Most apps use the real SDK in debug builds and the no-op in release builds.

## Use the no-op in release

```kotlin
dependencies {
    debugImplementation("gg.buoy:buoy-android:0.1.0")
    releaseImplementation("gg.buoy:buoy-android-noop:0.1.0")
}
```

`buoy-android-noop` has the same setup calls as `buoy-android`.
They do nothing and return right away.
So your setup code builds in both, with no `if` around it.

The no-op adds no Compose and no Buoy screens to your release APK.
It adds a little code that keeps a few calls working the normal way:

- `BuoyNetwork.interceptor` passes each call straight on.
- `BuoyClock.nowMs()` gives the real time.
- `BuoyPermissions.check` and `request` give Android's real answer.
- `BuoyNotifications.notify` posts the notification as it is.
- `Buoy.start` returns `null`.

To tell "blocked" from "never asked", the no-op keeps a small `gg.buoy.permissions` preferences file.

The real SDK adds the `INTERNET` and `POST_NOTIFICATIONS` permissions. The no-op adds none.
If your app posts its own notifications, put `POST_NOTIFICATIONS` in your own manifest.
Otherwise it works in debug and fails in release.

## Code that only debug builds have

The no-op only covers setup calls.
These are not in it:

- `BuoyAskBuoy.configure` and `AskBuoyConfig`.
- Custom tools: `BuoyTool` and `BuoyDevTools.register`.
- `BuoyAsyncImage` and `Modifier.buoyTrackRenders`. They need Compose.
- The Ask Buoy chat parts, like `AskBuoyHeader`, `MessageList` and `Composer`.

Put code that uses them in `src/debug`.
Then the release build never sees it.

## Ship Buoy to testers on purpose

You can put the real SDK in an internal build, like a QA build.
Give that build type its own line, so it gets the real SDK and not the no-op:

```kotlin
dependencies {
    debugImplementation("gg.buoy:buoy-android:0.1.0")
    "qaImplementation"("gg.buoy:buoy-android:0.1.0")
    releaseImplementation("gg.buoy:buoy-android-noop:0.1.0")
}
```

A build that is not debuggable needs a Pro plan.
Guard `Buoy.start` with your own check, so only your testers get it.

If that build turns on R8, keep your `BuildConfig` fields, or the Env tool shows nothing:

```
-keepclassmembers class com.example.app.BuildConfig { public static final <fields>; }
```

An environment badge or a user role is a label, not a lock.
Use your app's own checks for who can see or change its data.

## Features only for debug builds

Some actions check the build again, even when Buoy runs.
They need a debuggable build:

- Moving the Desktop address with `adb shell setprop debug.buoy.socketUrl`.
- Ask Buoy's simulations, like faking a server answer or driving the screen.
  In other builds, Ask Buoy can still read the app, impersonate and move between screens.
