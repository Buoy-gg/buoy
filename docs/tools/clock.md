---
title: Clock
seoTitle: "React Native Clock Override: fake the date and time in your app"
id: tools-clock
description: "Move your React Native app's clock to any date, freeze it or speed it up to test expiring offers, countdowns and renewals, and test sign-in token refresh, without changing the device's system time."
---

<!-- ::platform-badge platform="both" -->

<!-- ::tool-film id="clock" -->

Some bugs only show up at a certain time, like an offer that ends on Friday. Clock changes the time your app sees, and only your app. Jump a week ahead and see what breaks. Your phone's clock stays the same.

<!-- ::clock-live-demo -->

## Install

<!-- ::PM npm="npm install @buoy-gg/clock" yarn="yarn add @buoy-gg/clock" pnpm="pnpm add @buoy-gg/clock" bun="bun add @buoy-gg/clock" -->

Clock shows up in your `FloatingDevTools` menu on its own. If your app reads the time while it starts up, import it first in your entry file:

```js
// index.js
import "@buoy-gg/clock";
import "expo-router/entry";
```

## How to use it

- Tap +1 hour, day, week or month to jump ahead. Timers that would have gone off during the jump run too.
- Tap Date & Time to type an exact time, like `2026-12-31 23:59` or `+3d`.
- Turn on Freeze to stop time. Use Speed to make it run faster. (Pro)
- Tap Use Real Time to go back.

A small strip stays on screen while the time is changed, so you don't forget.

If a screen still shows the old time, it hasn't drawn again yet. Tap Reload App in the tool. The changed time stays through that reload.

With Pro, the changed time also stays when the app restarts or reloads any other way. On the free plan, that puts the app back on real time.

## Sign-in tokens

Your app sends a token with its requests to prove who the user is. Tokens run out, and then the app has to get a new one. Clock shows your app's token and how long it has left. It needs the [Network tool](./network) installed.

Two buttons test the refresh. Both are part of Buoy Pro:

- Jump to Expiry moves the app's time to just before the token ends. Your app should get a new token.
- Fail Next Request makes the next request fail with a 401 ("not signed in"). Your app should get a new token and try again.

Use your app after either one. A line under the token tells you if a new token came. Clock never shows or saves the token itself.

## What stays real

Clock changes `Date.now()`, `new Date()` and your app's timers. The time zone, animations, native code and your server keep real time. To test something your server checks, use [Network overrides](./network) instead.

## FAQ

### What is free?

Jumping ahead, Date & Time and the token row. Freeze, Speed, keeping the time after a restart, the two token tests, the MCP server and Ask Buoy need Pro.

### Does this change the time on my phone?

No. Only your app sees the new time.

### Can I change the time zone?

No. Change it in the simulator or phone settings.

### Can AI agents use it?

Yes. The [MCP server](../mcp) has `get_clock` and `clock_action`, and [Ask Buoy](./ask-buoy) can do it when you ask, like "show me the offers screen 30 days from now". Buoy Desktop has a Clock panel too.
