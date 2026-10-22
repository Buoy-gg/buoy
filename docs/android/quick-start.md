---
title: Quick Start
seoTitle: "Android DevTools Setup — add Buoy to a Kotlin app"
id: android-quick-start
description: "Add Buoy to an Android app in a few minutes: add the Maven package, start Buoy in a debug build, and check a captured web call."
---

This guide adds Buoy to an Android app.
Then it checks that a web call shows up.

## 1. Add the package

Buoy has its own Maven server at `maven.buoy.gg`.
Add it to the repositories in `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven("https://maven.buoy.gg") { content { includeGroup("gg.buoy") } }
    }
}
```

Then add Buoy to your app module's `build.gradle.kts`:

```kotlin
dependencies {
    debugImplementation("gg.buoy:buoy-android:0.1.1")
    releaseImplementation("gg.buoy:buoy-android-noop:0.1.1")
}
```

Debug builds get the real tools.
Release builds get a no-op copy that does nothing.
See [Release Builds](./release-builds) for why.

## 2. Add your account key

Put your key in `local.properties`. Keep that file out of git.

```properties
buoy.key=YOUR-KEY
```

Then pass it to the app as a build field.
Name it `BUOY_LICENSE_KEY`. The Env tool always hides a value with that name.

```kotlin
import java.util.Properties

android {
    defaultConfig {
        val local = Properties().apply {
            rootProject.file("local.properties").takeIf { it.exists() }?.inputStream()?.use { load(it) }
        }
        buildConfigField("String", "BUOY_LICENSE_KEY", "\"${local.getProperty("buoy.key", "")}\"")
    }
    buildFeatures { buildConfig = true }
}
```

## 3. Start Buoy

Call `Buoy.start` in your `Application` class.
Do it as early as you can.

```kotlin
import android.app.Application
import gg.buoy.Buoy
import gg.buoy.core.BuoyConfig

class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        Buoy.start(this, BuoyConfig(licenseKey = BuildConfig.BUOY_LICENSE_KEY.ifEmpty { null }))
    }
}
```

Name the class in your manifest with `android:name=".MyApp"`.
Buoy draws its button over every screen by itself.
You don't add a view for it.

## 4. Add the network hook

Buoy sees calls made with OkHttp.
Add its interceptor where you build your client:

```kotlin
val client = OkHttpClient.Builder()
    .addInterceptor(BuoyNetwork.interceptor)
    .build()
```

Retrofit, Coil and Glide all use OkHttp.
Give them this client and Buoy sees their calls too.
Build the client after `Buoy.start` runs.

## 5. Let Buoy reach your computer

Buoy talks to Buoy Desktop over plain HTTP.
Android blocks plain HTTP by default.
Allow it for your computer only, and only in debug builds.

Make `app/src/debug/res/xml/buoy_network.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="false">localhost</domain>
        <domain includeSubdomains="false">10.0.2.2</domain>
    </domain-config>
</network-security-config>
```

Point to it from `app/src/debug/AndroidManifest.xml`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application android:networkSecurityConfig="@xml/buoy_network" />
</manifest>
```

If your app has its own network security config, add these domains to it instead.
This file turns off `usesCleartextTraffic`.
If your app uses plain HTTP to its own dev server, add that address here too.

## 6. Check the first call

Run the app on the emulator.
Tap the Buoy button and finish any account prompt.
Make a web call in your app.
Then open Network and tap the call.
You see its address, status and answer.

If the call is missing, check three things:

- The client has `BuoyNetwork.interceptor`.
- The call ran after Buoy started.
- Capture is on in Network.

## 7. Open it in Buoy Desktop

Open [Buoy Desktop](../desktop) on your computer and sign in.
Then run this once for the emulator:

```bash
adb reverse tcp:42831 tcp:42831
```

The app shows up in Desktop.
For a real phone, see [Physical Devices](./devices).
