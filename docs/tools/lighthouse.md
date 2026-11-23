---
title: Lighthouse
seoTitle: "Buoy Lighthouse: run Google Lighthouse on every page of your web app"
id: tools-lighthouse
description: "Run Google Lighthouse on every page of a web app. Buoy finds the pages, tests many at once on slim Chromes, and gives each page four scores. Use it from Buoy Desktop, your AI agent or a terminal."
---

Buoy Lighthouse runs Google Lighthouse on every page of your web app. You give it your site. It finds the pages and tests each one.

Each page gets four scores from 0 to 100. They are for speed, access, best practices and SEO. You also see which checks failed on each page.

It's free with a Buoy account. It runs on your computer, on the same slim Chromes as the [Chrome Farm](./chrome-farm).

## What you need

- Node 22.19 or newer. Google Lighthouse needs it.
- A small Chrome, about 100 MB. The first run gets it for you.

## Use it in Buoy Desktop

1. Open the Lighthouse tab.
2. Type your site, like `localhost:3000` or `example.com`.
3. Press Scan site.

Pages show up in the table as each one ends. Click a page to see its failed checks. Pick an older run to see what changed.

## Use it from your AI agent

The Buoy MCP has six tools.

| Tool | What it does |
|---|---|
| `lighthouse_run` | Tests a page, a list of pages or a whole site |
| `lighthouse_status` | Shows how far a run is, and lists saved runs |
| `lighthouse_page` | Shows what failed on one page |
| `lighthouse_compare` | Shows what changed between two runs |
| `lighthouse_stop` | Stops the run this session started |
| `lighthouse_app` | Scores the screens of a connected phone app |

The first scan gets the Lighthouse engine. It is about 90 MB and is saved in `~/.buoy/lighthouse`. Later scans start at once.

You can ask your agent in plain words:

```text
Run Lighthouse on http://localhost:3000 and fix the three worst pages.
```

## Phone apps

Google Lighthouse can't test a native app, so Buoy brings its own checks for those. They work on a connected React Native app.

In Buoy Desktop, pick the app in the device list. Open the Lighthouse tab and press Scan app.

Or ask your agent to run `lighthouse_app`.

```text
Run lighthouse_app on my app and fix what fails on the most screens.
```

Buoy goes to each screen the app's router lists. It waits until the screen stops loading. Then it scores the screen for speed, access and best practices. At the end it puts the app back where it was. Our example app has 87 screens. That scan takes about 95 seconds.

It works with Expo Router and with plain React Navigation.

There are 52 checks. Here are some of them.

- Buttons with no name, no role or a small tap target. `hitSlop` counts.
- A button that is turned off, or a switch that is on, and does not say so to a screen reader.
- A slider or a progress bar you draw yourself that does not say its value.
- An icon button that is hard to see. It needs 3 to 1 against what is behind it.
- A screen with no heading. The phone's own top bar counts as one.
- Text that is hard to read against its background, too small, or can't grow. Placeholder text counts when your app sets its colour.
- Text that is cut off, or bigger than its button. Text that may run into other text is listed as a tip.
- Text the app has that can't be seen on screen. This is a tip, and it needs `pictures: true`.
- A screen that is slow to be ready, or makes the same web call twice.
- A blocked JS thread, heavy React renders and wasted renders.
- Web calls that fail or skip HTTPS, and secrets in addresses, logs or plain storage.
- A permission prompt as the screen opens.
- A text box that may end up under the keyboard. This one is a tip, since Buoy does not open the keyboard.

Some checks are tips. A tip is a list for you to look at, and it never moves a score. These are the newer ones.

- Buttons on one screen that share a name, like five that say "Edit".
- A row that opens and shuts and does not say which it is.
- A sheet your app draws that lets a screen reader reach what is behind it.
- A screen that keeps moving by itself.
- An app that stays upright when the phone is on its side.
- A video with no text tracks.
- A row with swipe actions and no other way to do them.

A phone screen can stop a scan in ways a web page can't. Buoy handles these.

- A system dialog opens over the app. Buoy taps "Don't Allow" and goes on. On a real iPhone it finds the button by reading a picture of the screen. Where it can't tap, it starts the app again.
- A screen crashes the app, or throws as it draws. Buoy saves the error, starts the app again and goes on.
- A screen crashes in native code. Buoy reads the phone's crash log and tells you why.
- A path has no screen. Buoy sees the app's not-found page and does not score it.
- A sign-in wall covers every screen. When a big part of the screens look just the same, Buoy tells you.
- A screen needs a value, like `/item/[id]`. Buoy uses one from the app's history. With none, it leaves the screen out and tells you.
- A screen sends you to a sign-in wall. Buoy does not score it, and tells you where the app went.
- A screen says sign out or delete. Buoy leaves it out unless you ask.

Good to know for apps:

- Speed numbers are true for that phone and that build only. A dev build is slower.
- Update the Buoy packages in your app for the full set of checks. An older Buoy can't report text colours, roles or `hitSlop`.
- It works on iOS and Android, on sims and on real phones.
- With plain React Navigation, give a screen its params in the path, like `/Profile?name=sam`.
- Ask for `appearance: "dark"` to scan in dark mode. Ask for `textSize: "large"` to scan with big text. Buoy sets the phone, reloads your app, scans, and puts it all back. The reload lays each screen out fresh, the way a person with that setting sees it. If Buoy can't read the old setting, it changes nothing. This works on an iOS sim, on Android and on a real iPhone.
- Ask for `motion: "reduce"` to scan with Reduce Motion on. A screen that still moves is then listed. Buoy puts the setting back after. This works on an iOS sim, on Android and on a real iPhone.
- Buoy turns the phone on its side once in each scan, to see if your app turns too. Then it turns it back. Pass `turn: false` to skip that.
- A spinner you draw yourself should have `accessibilityRole="progressbar"` and `accessibilityState={{ busy: true }}`. Then Buoy waits for it, the way it waits for React Native's own spinner.
- No tool finds every access problem. Buoy does not check the order a screen reader reads in, or where focus lands. Try your app with VoiceOver and TalkBack too.
- Tokens and passwords that Buoy can spot are taken out of the report before it is saved. Buoy also learns them as it scans. A token it saw in a web call is then hidden as plain text on a screen too.
- Buoy knows where the words of a text end. It asks React Native, so it does not guess from the text's box.
- Ask for `pictures: true` to also check each screen by eye. Buoy takes a picture and reads the words in it. It lists text the app has that can't be seen. This needs a Mac and adds about half a second a screen.
- Each score says how much of it was tested. A Buoy tool your app lacks leaves a hole, and Buoy tells you how big it is.
- Your agent can tap a system dialog away on a sim. Buoy Desktop can't, so a scan from Desktop stops there and says why.

## How it finds pages

It looks in four places, in this order:

1. The pages you list.
2. The sitemap. It reads `robots.txt` to find it.
3. The app's own router. This works for Vue Router today.
4. Links on each tested page.

It follows the rules in `robots.txt`. Pages that look alike are sampled. `/blog/a` and `/blog/b` are one group, and it tests 8 pages from each group. It stops at 200 pages. Your agent can change these numbers.

## Pages behind a sign-in

Your agent can pass cookies, headers or `localStorage` values to `lighthouse_run`. Buoy sets them before each page loads. They are not saved with the run.

## Speeds

| Speed | What it does |
|---|---|
| Exact | Uses Lighthouse's own waits, to the letter. It is the slowest. |
| Smart | The default. The wait times are the same, but a page that is already quiet moves on. |
| Quick | Waits less on each page. It is the fastest, but it can miss things that happen late. |

## Why it is fast

A page spends most of its time waiting for Lighthouse to see it go quiet. A page that waits needs almost no CPU. So Buoy keeps up to 16 Chromes open and lets only a few pages load at the same time. Each loading page gets a whole core, which keeps its speed score true.

On our test Mac, 24 pages took 8.2 s at Exact, 5.7 s at Smart and 3.6 s at Quick. Unlighthouse took 39.9 s for the same pages with the same Lighthouse settings. The scores matched.

## Good to know

- Speed scores from a dev server are much worse than from a real build. Test a production build for true speed numbers.
- Each Chrome draws on the CPU. That keeps numbers steady when many Chromes run. Pages built on WebGL draw slowly this way.
- The slim Chrome does not ask for a site's favicon. So a missing favicon is not flagged.
- A site that routes with `#` in the address is tested as one page.
- Runs are saved in `~/.buoy/lighthouse/runs`. Buoy Desktop and your agent see the same runs. The newest 30 are kept.
