---
title: Installation
seoTitle: "Install Buoy for Android — Gradle setup for Kotlin apps"
id: android-installation
description: "Install Buoy for Android with Gradle, sign in to your Buoy account, and set up Buoy in a Compose or View app."
---

## What you need

- An app with `minSdk` 23 or higher and `compileSdk` 35 or higher. Debug builds need `minSdk` 24.
- Kotlin 2.0 or newer, if the app uses Kotlin.
- Android Gradle Plugin 8.8 or newer.
- A Free or Pro [Buoy account](https://buoy.gg).

Buoy works in Compose apps and in apps built with plain views.
It brings in Jetpack Compose and OkHttp for its own screens.
It also adds two permissions to builds that use it.
`INTERNET` lets it reach Desktop and check your account.
`POST_NOTIFICATIONS` lets the Notifications tool send test alerts.
The no-op adds no permissions.

Is your app's `minSdk` 23?
Then raise it to 24 in each build that uses Buoy.
This keeps release at 23:

```kotlin
androidComponents {
    beforeVariants(selector().all()) { if (it.buildType != "release") it.minSdk = 24 }
}
```

In a Groovy `build.gradle`, the same lines work as they are.
Your release build can stay at 23.
The no-op works on 23.
Java code on 23 can't use the `Supplier` form of `BuoyBench.configure`.
That type needs API 24.
Use the other form, or turn on core library desugaring.

## Add the package

Buoy has its own Maven server at `maven.buoy.gg`.
Add it to your repositories in `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven("https://maven.buoy.gg") { content { includeGroup("gg.buoy") } }
    }
}
```

In a Groovy `settings.gradle`, use this line instead:

```groovy
maven { url 'https://maven.buoy.gg'; content { includeGroup 'gg.buoy' } }
```

The `includeGroup` part means Gradle asks our server for Buoy and nothing else.
Does your app list repositories in its root `build.gradle` instead?
Then add the same line there.

Version 0.1.0 is also on Maven Central.
New versions come out on `maven.buoy.gg` only.

Then add Buoy to your app module:

```kotlin
dependencies {
    debugImplementation("gg.buoy:buoy-android:0.1.3")
    releaseImplementation("gg.buoy:buoy-android-noop:0.1.3")
}
```

`buoy-android` holds every tool.
`buoy-android-noop` has the same setup calls, and they do nothing.
See [Release Builds](./release-builds).

While Buoy is 0.x, a new minor version can break things.
Pin the version you test with.

### If your build checks licenses

Some apps check the license of every package they use.
One common tool for this is Licensee.
Buoy is not open source, so its license is a link to our terms.
Add that link to your allowed list:

```kotlin
licensee {
    allowUrl("https://buoy.gg/terms")
}
```

### If your build checks package hashes

Some apps list a hash for every package they trust.
Gradle calls this dependency verification.
Add Buoy's packages to that list when you add Buoy.

### If your app ships its own copy of AndroidX

Buoy uses AndroidX.
Some apps bundle their own copy of AndroidX parts under the same names.
Then the build fails with "Duplicate class androidx...".
Leave out your copy in debug builds, where Buoy runs.

## Sign in

Run this in your project folder:

```bash
npx --package=@buoy-gg/core buoy login
```

Your browser opens buoy.gg. Click Allow.
Then the command puts a dev token in `local.properties` as `buoy.key`.
The [Quick Start](./quick-start) has the Gradle lines.
They pass the token to the app as `BuildConfig.BUOY_LICENSE_KEY`:

```kotlin
Buoy.start(this, BuoyConfig(licenseKey = BuildConfig.BUOY_LICENSE_KEY.ifEmpty { null }))
```

A Free account works in debug builds.
The token lasts 30 days.
Then run `buoy login` again, and build the app again.
Keep `local.properties` out of git.

### Test builds

QA and internal test builds sign in with a code.
That way, no key goes in the app.
Turn it on with `signIn`:

```kotlin
Buoy.start(this, BuoyConfig(signIn = BuoySignInConfig()))
```

First, add your app at [buoy.gg/dashboard/sites](https://buoy.gg/dashboard/sites).
Type `app:` and then your package name, like `app:com.acme.shop`.
Buoy reads your package name by itself.
To use a different id, pass `BuoySignInConfig(appId = "...")`.

In the app, tap **Sign in**.
Scan the QR code with a phone that is signed in to buoy.gg.
Or go to buoy.gg/activate and type the code.
Then tap Allow.

A build that is not debuggable needs a Pro or Business plan.
A dev token does not work there.
[Release Builds](./release-builds) shows how to put Buoy in a test build.

### Keys still work

You don't have to sign in.
Your account key from [buoy.gg/dashboard/licenses](https://buoy.gg/dashboard/licenses) still works.
Put it in `local.properties` as `buoy.key`, or use any way your app gets config.
Keep the key out of git.

For CI and test runs, use a bot key.
A team admin makes one on the Team page.
See [Keys for bots and CI](../sign-in#keys-for-bots-and-ci).

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
| `licenseKey` | The token from `buoy login`, or your account key. |
| `signIn` | Lets people sign in with a code. |
| `telemetry` | One anonymous ping a day, in debug builds only. |
| `enableLogs` | Prints Buoy's own log lines. |
| `extraDeviceInfo` | Extra lines for the device card in Desktop. |
| `allProcesses` | Starts Buoy in every process, not just the main one. |

Java code can call `Buoy.start(context)` or `Buoy.start(context, config)`.

## Buoy Desktop

Open [Buoy Desktop](../desktop) and sign in.
On the emulator, Buoy looks for it at `http://10.0.2.2:42831`.
That address is your computer.
Your network config must allow plain HTTP to it, as the Quick Start shows.
If it allows only `localhost`, run `adb reverse tcp:42831 tcp:42831` once.

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
