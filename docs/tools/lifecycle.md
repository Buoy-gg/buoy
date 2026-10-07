---
title: Lifecycle
seoTitle: "React Native Lifecycle Simulator: test background, interruptions and relaunch"
id: tools-lifecycle
description: "Send your React Native app to the background, interrupt it like a phone call, send a memory warning or a deep link, change battery and dark mode, or relaunch it, then see what your code did in response."
---

<!-- ::platform-badge platform="both" -->

On a real phone your app gets interrupted all the time. People switch apps, take a call, come back an hour later, or the system kills the app and they open it again. Lifecycle lets you do those things to your app from inside it, then shows you what your code did.

Lifecycle is part of Buoy Pro.

## Install

<!-- ::PM npm="npm install @buoy-gg/lifecycle" yarn="yarn add @buoy-gg/lifecycle" pnpm="pnpm add @buoy-gg/lifecycle" bun="bun add @buoy-gg/lifecycle" -->

Lifecycle shows up in your `FloatingDevTools` menu on its own. To see what the app did after each test, also install the [Events tool](./events). To skip the wait on long tests, install [Clock](./clock).

## How to use it

Open Lifecycle and pick a moment under Test a moment:

- Take a phone call. The app pauses for 20 seconds, then comes back.
- Switch apps for a moment. The app goes to the background for 5 seconds.
- Come back in 31 minutes, or the next morning. With [Clock](./clock) installed, the app clock jumps forward, so you don't wait. This is how you test "sign in again after 30 minutes away".
- Run low on memory. A memory warning, then 10 seconds in the background.
- Battery almost empty. 5% with Low Power Mode on. This only shows when your app has `expo-battery` or `react-native-device-info`.
- Close and reopen the app. Restarts the app's JavaScript and checks that it reopens on the screen you were on.
- Open a link. The field starts with your app's own scheme. The app gets the link while it's running, the same way it would from a notification or another app.

While the app is away, the Lifecycle sheet gets out of the way, so you can see what your app shows, like a privacy cover or a lock screen. A small strip at the top counts the time and has a Return button. When the app comes back, the sheet opens on the result.

For anything else, open Custom. There you can leave the app for a set time, send a memory warning, switch between System, Light and Dark appearance, set the battery, or press the Android back button. The back button never closes the app; the result tells you if a real press would have.

## The result

After each test, the top of the tool says what happened first, then shows the evidence:

- "Kept making requests in the background" when the app kept working after its first second away. If the requests come from React Query, it tells you to connect `focusManager` to AppState.
- "Nothing in your app listens for …" when your code never reacts to that event.
- For Close and reopen, whether the app came back to the screen you were on, with a button to go back there if it didn't.

Under that, the network requests, React Query updates, route changes and storage or store writes are listed for while the app was away and after it came back. Repeats are grouped, like `GET /api/offers ×3`. The list needs the [Events tool](./events).

While anything is simulated, like Dark mode or a low battery, a Stop Simulating button puts the real values back.

## What a simulation can't do

The tool sends your app the same events the phone sends, so your own code runs the way it would on a phone. It doesn't pause anything, though. Timers, requests, animations and native code keep running while the app is "in the background". If you really leave the app during a test, the real event wins and the simulation ends.

Relaunch restarts JavaScript only. The native app keeps running, so it tests what your JavaScript saves and restores. To test a real cold start, use the MCP server's `real` option below.

## FAQ

### Does my app know it's a test?

No. It gets the same events it gets on a phone. Buoy's own tools ignore simulated events, so they keep running.

### Can I cause the real events?

On an iOS simulator or Android, yes. The [MCP server](../mcp)'s `lifecycle_action` takes `real: true` for background, return, relaunch, color scheme and deep links. It uses `xcrun simctl` or adb, so relaunch really kills the app. It can also `quit`, `launch` and `reinstall` the app. Memory warnings are simulated only for now.

### Can AI agents use it?

Yes. The MCP server has `get_lifecycle` and `lifecycle_action`, and [Ask Buoy](./ask-buoy) can run it when you ask, like "check what happens if I leave checkout for 31 minutes". Buoy Desktop has a Lifecycle panel too.
