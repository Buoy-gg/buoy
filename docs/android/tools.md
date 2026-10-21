---
title: Tools
seoTitle: "Buoy for Android tools — network, storage, console and more"
id: android-tools
description: "What each Buoy tool does in a native Android app, how to connect it to your app, and what the Android version does not do yet."
---

`Buoy.start` turns on every tool.
Some tools see your app right away.
Others need a line of code to reach your app's data.
Each part below says how to connect the tool, and what Android leaves out next to React Native.

## Network

Add `BuoyNetwork.interceptor` to your OkHttp client:

```kotlin
OkHttpClient.Builder().addInterceptor(BuoyNetwork.interceptor).build()
```

Retrofit, Coil and Glide run on OkHttp, so their calls show up too.
`HttpURLConnection` and Ktor's CIO engine are not seen.

Fake answers can change how the app acts.
Try them on a call you can throw away.
Turn them off when you are done.

Slow-network modes live in Network's menu.
They work while Network is shrunk or capture is paused.

## Console

Console shows the app's own log lines from `Log` and `println`.
It reads them with `logcat` for the app's own process, so it needs no permission.
Android's own system lines are left out.

`BuoyConsole.log(...)` keeps the values you pass and where the call came from, like `console.log` in React Native.

## Storage

Storage shows the app's default SharedPreferences file.
Add other files by name:

```kotlin
BuoyStorage.registerSharedPreferences("settings")
```

For MMKV, hand over a small adapter with `BuoyStorage.registerMMKVInstance(...)`.
For a secure store, use `BuoyStorage.registerSecureStore(store, keys)`.
`BuoySecureStore.of(prefs)` turns an `EncryptedSharedPreferences` into one.
Buoy adds no MMKV or security library to your app.

`BuoyStorage.configure(requiredKeys = ...)` lists the keys your app expects.

## Env

```kotlin
BuoyEnv.configure(
    vars = BuoyEnv.buildConfigValues(BuildConfig::class.java),
    required = listOf(BuoyRequiredEnvVar.type("API_URL", "url")),
)
```

`buildConfigValues` reads the fields in your `BuildConfig`.
`BUOY_LICENSE_KEY` is always hidden.

## Impersonate

`BuoyImpersonate.configure(onSearchUsers = ...)` lets Buoy search your users.
Buoy adds a header to calls that go through its interceptor.
Your server must check that the change is allowed.

Buoy can't clear your app's saved data for it.
Pass `onClearData` and your app does that part.

## Image Overlay

Lay a design image over the app.
To line it up with a view, tag the view with `Modifier.testTag("image-target:Profile photo")`.
Or drag the image anywhere.

## Images

Use `BuoyAsyncImage` to test load, fail, retry, blank and swap states:

```kotlin
BuoyAsyncImage(model = imageUrl, contentDescription = "Avatar", modifier = Modifier.size(64.dp))
```

`BuoyAsyncImage` uses Coil 3.
Your app needs `io.coil-kt.coil3:coil-compose` and `io.coil-kt.coil3:coil-network-okhttp` to load web images.

To see plain Coil images too, add Buoy's hook to your `ImageLoader`:

```kotlin
ImageLoader.Builder(context)
    .components { add(BuoyImagesCoil.interceptor) }
    .eventListenerFactory(BuoyImagesCoil.eventListenerFactory)
    .build()
```

Per-image buttons, like reload or force an error, only work on `BuoyAsyncImage`.
Coil doesn't report download progress.

## Notifications

```kotlin
BuoyNotifications.install(this)                       // Application.onCreate
BuoyNotifications.observeDeviceToken(token)           // FirebaseMessagingService.onNewToken
BuoyNotifications.observeRemoteMessage(id, title, body, data)  // onMessageReceived
BuoyNotifications.notify(context, id, notification)   // in place of NotificationManager.notify
```

Buoy can't see a direct call to `NotificationManager.notify`.
A test alert set for later is lost if the app is killed first.

## Routes

Give Buoy your routes and your navigation:

```kotlin
BuoyRoutes.configure(
    routes = listOf("/", "/pokemon/[name]", "/settings"),
    navigate = { href, replace -> router.go(href, replace); true },
    goBack = { router.back() },
    stack = { router.entries.map { BuoyRoutes.stackItem(it.id, it.href) } },
)
```

`navigate` returns `true` when it moved the app.
Call `BuoyRoutes.refreshStack()` when your stack changes.
With no stack to hand over, call `BuoyRoutes.screenAppeared(path)` on each screen.

## Scenarios

Ship a QA menu of saved app setups:

```kotlin
BuoyScenarios.configure(currentBuild = BuildConfig.VERSION_NAME)
BuoyScenarios.defineAll(assets.open("scenarios.json").reader().readText())
```

Recording a new scenario is not on Android yet.

## Time Machine

Time Machine saves SharedPreferences, MMKV, secure keys and the current screen.
Add your own state with a provider:

```kotlin
BuoyTimeMachine.registerProvider(BuoySnapshotProvider(id = "cart", label = "Cart", capture = { ... }, restoreItem = { ... }))
```

A reload builds the screen again. The app process keeps running.
Pass your own reload to `BuoyTimeMachine.configure(onReload = ...)` for a full restart.

## Events

Events shows network, storage and route activity in one list.
It has no Redux, Zustand, Jotai, React Query or render source.

## Assets

Assets reads the installed APK.
It shows sizes, copies and baselines.

The Images tool marks assets it sees load.
Mark anything else with `BuoyAssets.markLoaded(...)`.
Load fonts with `BuoyAssets.loadFont(context, path)` so they show as loaded.

## Clock

Android can't change the system clock for you.
Read time through Buoy, and the Clock tool can move it:

```kotlin
val expired = BuoyClock.nowMs() >= session.expiresAt
BuoyClock.after(60.0) { showReminder() }
```

System time stays real, and so do animations.

## Lifecycle

Hand over the hooks where real and fake events both land:

```kotlin
BuoyLifecycle.configure(
    appState = { state -> session.onAppState(state) },
    deepLink = { url -> router.open(url) },
    colorScheme = { scheme -> theme.override = scheme },
)
```

Leaving the app sends "inactive" and then "background", like iOS.
A reload builds the screen again. The app process keeps running.

## Location

Read location through `BuoyLocationProvider`:

```kotlin
val location = BuoyLocationProvider(context)
location.onLocations = { fixes -> map.show(fixes.last()) }
location.startUpdatingLocation()
```

With the tool off, every call goes to the device.
Real fixes still need your app's own location permission.
Heading comes from the fix's bearing, not a compass.

## Permissions

Check and ask through Buoy:

```kotlin
when (BuoyPermissions.check("camera")) {
    "granted", "limited" -> openCamera()
    "undetermined" -> if (BuoyPermissions.request("camera") == "granted") openCamera()
    else -> showSettingsButton()
}
```

Android can't tell "never asked" from "blocked".
Buoy remembers the asks that go through it.

## Bench

Bench shows a speed HUD and saves test runs you can compare.
Batch runs move through the app with `BuoyRoutes.configure`.

Android has no Reanimated, so that row is gone.
Compose gives no render times.

## Highlight

Mark the views you want counted:

```kotlin
Column(Modifier.buoyTrackRenders("CartTotal", props = mapOf("total" to total)))
```

Only marked views are counted. React Native sees every view.
Render times and hook details are not there.

## Debug Borders

Press BORDERS in the floating row.
It cycles through outlines, labels and off.
Borders show on views that have a test tag or a label.

## Ask Buoy

Point Ask Buoy at your model endpoint:

```kotlin
BuoyAskBuoy.configure(AskBuoyConfig(endpoint = "https://ai.example.com/v1/messages", model = "your-model"))
```

Ask Buoy can't take real files yet, on Android or React Native.
Its "Choose a file" button sends a stand-in name.
