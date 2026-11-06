---
title: Bug Links
seoTitle: "React Native bug reports in one tap: a locked link your team can load"
id: tools-bug-links
description: "Report a bug in your React Native app with one tap. Buoy saves the app's state, web calls, taps and screen, takes out private data, locks it on the phone, and gives you a link. A teammate opens the link and loads the same bug in their own build."
---

<!-- ::platform-badge platform="both" -->

A tester sees a bug. They tap the bug button once. Buoy copies a link to the bug. Your team opens the link and sees the same bug, in the same state.

No one has to write steps down. No one has to guess what the app was doing.

## Install

<!-- ::PM npm="npm install @buoy-gg/bug-links" yarn="yarn add @buoy-gg/bug-links" pnpm="pnpm add @buoy-gg/bug-links" bun="bun add @buoy-gg/bug-links" -->

Bug Links shows up in your `FloatingDevTools` menu on its own. To keep the bug button next to the Buoy bubble, so a report is one tap from any screen, add it to the floating row:

```tsx
<FloatingDevTools defaultFloatingTools={["report-bug"]} />
```

Bug Links needs safe random numbers to lock a bug. Every Expo app already has them. A plain React Native app needs one of these: `react-native-get-random-values` or `react-native-quick-crypto`.

## Report a bug

Tap the bug button (REPORT BUG). That's it. Buoy saves:

- the app's state: storage, Redux, Zustand, Jotai and React Query
- the last web calls and their answers
- the console, with errors
- the taps that led here, as plain steps
- what the screen said
- the screen you were on, the app's clock, and the phone's setup
- the app's version and build

Then a note pops up: **Bug link copied**. Paste it in Slack or your bug tracker. You can tap **Note** to say what went wrong. The link stays the same.

If the phone is offline, the bug waits on the phone. Buoy sends it once it's back online.

## Private data stays on the phone

Before anything leaves the phone, Buoy takes out private data. That includes passwords, tokens, keys, card numbers, emails and phone numbers. The note pop-up says how many it took out.

Then Buoy locks the bug for your team. Only people signed in to your team can open it, from the team list or with its link.

Text typed into fields never goes in a bug. A step says "Typed 7 characters into Email" instead. Values kept in SecureStore never go in a bug at all.

## Pictures

A bug also has pictures of the app. Buoy takes one after each tap. It takes one more right before you report. The bug page shows each picture next to its step. Tap one to see it big.

Private parts are hidden first, on the phone. A dark box goes over them before the picture is kept. By default Buoy hides:

- all text
- all images
- all text fields

Password fields are always hidden. On the web, card fields are too. A screen the app marks as private (Android's `FLAG_SECURE`) gets no picture at all.

To show less or more, set it once when the app starts:

```tsx
import { configureMedia } from "@buoy-gg/bug-links";

configureMedia({ maskAllText: false, maskAllImages: true, maskAllInputs: true });
```

To hide one part, wrap it. To show one part, wrap it too:

```tsx
import { BuoyMask, BuoyUnmask } from "@buoy-gg/bug-links";

<BuoyMask><Balance /></BuoyMask>
<BuoyUnmask><ProductName /></BuoyUnmask>
```

`BuoyMask` always wins over `BuoyUnmask`. On the web, add `data-buoy-mask` or `data-buoy-unmask` to any element. That works in Vue, Angular, Solid and plain HTML too. If your app already marks parts for Sentry Session Replay, Buoy uses those marks as well.

Phones need a dev build for pictures. In Expo Go, the bug says "Pictures need a dev build." Pictures also work in the browser. A picture is never taken of Buoy's own screens.

If a picture can't be taken, the bug says why. For example, it may say "a Buoy screen was open."

## Open a bug

Open the link in a browser. You'll see the steps, the screen, the errors and the web calls. Tap **Copy for AI** to paste the bug into an AI chat. The key stays out of what you copy.

## Load a bug in your app

This is the best part. Load the bug into your own build, and the bug shows up there:

- From Claude Code or Codex: on the bug page, tap **Copy link for Claude Code**. Then say "Load the bug link on my clipboard in my sim." Buoy's `load_bug_case` tool reads the link from your clipboard. So the key never goes in the chat.
- On a phone: open Bug Links, paste the link, and tap **Load this bug here**.

Buoy puts back the app's state, the clock and the screen. Every web call gets the answer the reporter's phone got. Any other call is blocked, so the bug's data never reaches your real servers.

A dark pill at the top of the screen shows that a bug is loaded. It looks like the one Impersonate shows. If both are on, the bug pill sits just under it.

- Tap **↻** after you fix the code. Buoy loads the same bug again, so you can see if it's gone.
- Tap **✕** to put your app back the way it was (Restore my app).
- Tap the bug's name to see what loaded. From there you can also tap **Use live requests** to let the app talk to your real servers again.

The pill turns orange when something didn't load or a call was blocked. It says what, like "1 request blocked".

Some bugs need the reporter's phone, not just their data. If your app version, language, time zone, dark mode or screen size isn't the same, the details say so. Then you know why a bug might not show.

Some apps read a value only when they start, like a feature flag. Turn on **Restart after loading**. Buoy restarts the app, then loads the bug again.

In a React Native, Swift, Android, .NET MAUI or Flutter release build, loading needs a paid plan and the Buoy menu on screen. Flutter must [opt in](../flutter/buoy-devtools#release-builds). Other platforms still need a dev build.

A release build can't load a bug's state into a plain Redux store. To change that, wrap your root reducer in `withBuoyDevTools()`. Restore still works. If the bug's screens saved data in that store, restart the app to clear it.

If your plan or sign-in ends while a bug is loaded in a release build, the recorded replies stop. Every web call stays blocked, so the bug's data can't reach real servers. A pill says so. Sign in to restore your app.

You can also tap the pill to read a warning, then press and hold it to use real calls. The bug's data stays in the app, and it can reach real servers. If no one does either, the block ends by itself after 24 hours.

## FAQ

### Who can make bug links?

Bug links are part of Buoy Solo and Business.

### How long does a link last?

90 days after anyone last opened it. Each open starts the 90 days again.

### What if I lose the link?

The phone that made it keeps a list of its own links, keys included. Open Bug Links on that phone to find it.

### Can Buoy read my bugs?

Bugs are locked on the phone. Buoy keeps your team's key, so signing in just works. Buoy never opens a bug unless your team asks.
