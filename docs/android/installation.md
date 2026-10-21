---
title: Installation
seoTitle: "Install Buoy for Android — Gradle setup for Kotlin apps"
id: android-installation
description: "Install Buoy for Android with Gradle, add your account key, and set up Buoy in a Compose or View app."
---

## What you need

- An app with `minSdk` 24 or higher and `compileSdk` 35 or higher.
- Kotlin 2.0 or newer, if the app uses Kotlin.
- Android Gradle Plugin 8.8 or newer.
- A Free or Pro Buoy account key. Get one in your [Buoy account](https://buoy.gg).

Buoy works in Compose apps and in apps built with plain views.
It brings in Jetpack Compose and OkHttp for its own screens.
It also adds two permissions to builds that use it.
`INTERNET` lets it reach Desktop and check your account.
`POST_NOTIFICATIONS` lets the Notifications tool send test alerts.
The no-op adds no permissions.

## Add the package

Buoy is on Maven Central.
Make sure `mavenCentral()` is in your repositories.
Then add Buoy to your app module:

```kotlin
dependencies {
    debugImplementation("gg.buoy:buoy-android:0.1.0")
    releaseImplementation("gg.buoy:buoy-android-noop:0.1.0")
}
```

`buoy-android` holds every tool.
`buoy-android-noop` has the same setup calls, and they do nothing.
See [Release Builds](./release-builds).

While Buoy is 0.x, a new minor version can break things.
Pin the version you test with.

## Account key

The [Quick Start](./quick-start) reads the key from `local.properties` into a `BuildConfig` field.
Any way your app gets config is fine.
Keep the key out of git.

```kotlin
Buoy.start(this, BuoyConfig(licenseKey = BuildConfig.BUOY_LICENSE_KEY.ifEmpty { null }))
```

A free account works in debug builds.
A build that is not debuggable needs a Pro plan.

To skip the key, let people sign in with a code instead:

```kotlin
Buoy.start(this, BuoyConfig(signIn = BuoySignInConfig()))
```

## Start Buoy

Call `Buoy.start` in `Application.onCreate`.
It runs on the main thread.
A call from another thread waits for the main thread.

Buoy starts every tool and draws its button over each screen.
You don't add a view or call an install step.

Start Buoy before you build OkHttp clients and other app parts that make web calls.

## Compose and views

Nothing changes between the two.
Buoy draws its own layer on top of each activity.
Taps that miss Buoy go to your app.

Some tools need a line in your UI code, like `BuoyAsyncImage` or `Modifier.buoyTrackRenders`.
[Tools](./tools) lists them.

## Settings

`BuoyConfig` takes these:

| Setting | What it does |
|---|---|
| `enabled` | Turns Buoy off when false. |
| `deviceName` | The name Desktop shows. |
| `deviceId` | Leave it unset. Buoy makes one id per install. A custom id must be different on each device. |
| `socketUrl` | Where Buoy Desktop is. See [Physical Devices](./devices). |
| `licenseKey` | Your account key. |
| `signIn` | Lets people sign in with a code. |
| `telemetry` | One anonymous ping a day, in debug builds only. |
| `enableLogs` | Prints Buoy's own log lines. |
| `extraDeviceInfo` | Extra lines for the device card in Desktop. |
| `allProcesses` | Starts Buoy in every process, not just the main one. |

Java code can call `Buoy.start(context)` or `Buoy.start(context, config)`.

## Buoy Desktop

Open [Buoy Desktop](../desktop) and sign in.
Buoy looks for it at `http://localhost:42831`.
On the emulator, run this once:

```bash
adb reverse tcp:42831 tcp:42831
```

For a real phone, see [Physical Devices](./devices).

Pick the Android device in Desktop.
Then check a web call again.
Being connected and seeing calls are two different checks.

## Stop Buoy

A second `Buoy.start` gives back the running copy.
It can set a new account key.
It does not change the Desktop address or the device id.

`Buoy.stop()` takes Buoy out of the app.
The button goes away and the link to Desktop closes.
Each tool stops changing what the app sees.
The network hook stays in your client, but it lets every call pass.
Saved tool settings stay, so a later `Buoy.start` picks them up.
