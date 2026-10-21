---
title: Sim Farm
seoTitle: "Sim Farm — try many speed fixes at once on iOS simulators and Android emulators with Buoy Optimize"
id: tools-sim-farm
description: "Run Buoy Optimize on many slim iOS simulators or Android emulators at once. Your AI agent gives each speed idea its own sim, checks looks and memory on all of them, then times the best on one quiet sim."
---

The Sim Farm runs many iOS sims and Android phones on one Mac for [Buoy Optimize](../optimize). Optimize has your AI agent build many ideas to make a slow screen faster. The farm gives each idea its own sim, so the agent can check them all at once. It's free with a Buoy account.

<!-- ::tool-film id="sim-farm" -->

## How it works with Buoy Optimize

Name the slow screen or component. Ask your agent:

```text
Use buoy optimize and the sim farm on our slow feed screen.
```

That's all you need to say. You don't pick how many ideas. The skill tells the agent to find as many as it can and test them all. Then it goes like this:

1. **Ideas.** The agent writes down every way it can think of to make it faster. That's at least 20 ideas.
2. **Build.** It builds the most likely ones first. Each one is its own version of the screen.
3. **Look.** The farm gives each idea its own sim. The agent opens each idea and takes a picture. An idea that crashes or draws the wrong thing is out.
4. **Memory.** Each sim acts like a real phone: an iPhone SE, or a 3 GB Android phone. An idea that uses more memory than the phone allows is out too. On a plain sim, you'd never see it.
5. **Speed.** The ideas that are left get timed on one quiet sim. The agent holds a lock, so no other test runs then. It times the old code in every batch too. If the Mac got too busy, the farm marks the run unfair, and the agent runs it again.
6. **Again.** The agent keeps what was faster and still looks right. It mixes the best ideas, builds new ones and tests again. It keeps going until it can't make it faster, or your time runs out.

Why only one sim for speed? All sims share the Mac's CPU. Ten sims timed at once would slow each other down. So many sims check looks and memory, and one sim checks speed.

You can watch it all in Buoy Desktop. Its **Sims** panel shows each sim live, with the idea on it and how it's going.

More prompts to try:

```text
Use buoy optimize and the sim farm on our slow map screen.
Use buoy optimize and the sim farm on the PriceChart component.
Use buoy optimize and the sim farm on the chat screen. Stop after 30 minutes.
```

## Start

1. Set up the [Buoy MCP](../mcp) in your editor. Run `npx -y @buoy-gg/mcp@latest init` in your app's folder. It also adds the `buoy-optimize` skill.
2. Add [Bench](./perf-monitor) (`@buoy-gg/perf-monitor`) to your app. Optimize uses it to time each idea.
3. Build your app as a development build: for the iOS simulator (the farm needs the `.app` file), or for Android (a debug `.apk`, see [Android](#android)).
4. Ask your agent to use buoy optimize and the sim farm on your slow screen or component.

The agent makes a template sim with your app on it. It takes the sims it needs and gives them back when it's done.

For iOS, you need a Mac with Xcode and an iOS sim runtime. For Android, you need the Android tools (see [Android](#android)). Buoy Desktop is not needed, but it lets you watch.

## Why slim iOS sims

A normal sim uses about 5 GB of memory. Most Macs can only run a few at a time. That's too few to try many ideas.

Farm sims are slim. Each one turns off phone services a test doesn't need. On our test Mac, that changed a lot:

| | Normal sim | Slim sim |
|---|---|---|
| Memory | 4.7 to 5.8 GB | 1.4 to 1.6 GB |
| Programs running | 210 to 257 | 77 to 84 |
| Start time | 39 s | 12 s |

Each new sim is a copy of the template, made in about 4 s. Ten slim sims started at once took 94 s and used 15.4 GB in all.

## What slim turns off

Slim sims have no push, purchases (StoreKit), app links, widgets or Spotlight. The Contacts, Photos and Calendar pickers may act odd. If your test needs one of these, the agent can make a normal ("stock") template instead.

The farm never touches a sim it didn't make. Your own sims stay as they are.

## How many sims fit

It depends on your Mac's free memory and disk space. The farm checks both before it makes a sim. It keeps room free for the Mac, so small Macs get sims too. `sim_farm_status` says how many more will fit.

Sims share the Mac's CPU. Many sims can sit open, but only a few can work hard at the same time. Each busy one slows the rest.

## Fair speed tests

A busy Mac can make a good fix look slow. So a speed test on the farm holds a lock while it runs. The farm watches the Mac the whole time. It marks a run as unfair if the Mac got hot, ran low on memory or had too few free cores. The agent runs those again and doesn't rank on them.

Farm numbers come from a slim sim on your Mac. Check the winner on a real phone before you ship it.

## Every iOS app acts like a real iPhone

A sim app is a Mac program, so it sees all of the Mac's memory. In our test, one app grew to 20 GB. A real iPhone SE closes an app at about 2.1 GB.

For Optimize, that hides a big problem. An idea can be fast because it keeps everything in memory. On a sim it looks great. On a real phone, the app gets closed.

So the farm starts each app as an iPhone SE (2nd generation):

- The app sees the phone's memory (2,957 MB) and 6 cores.
- It gets a memory warning near where the phone would warn (1,707 MB).
- Past the phone's limit (2,098 MB), it keeps running, and the farm notes that a real phone would have closed it. `sim_farm_status` shows the app's peak memory and `overLimit: true`.
- At a safety cap, the farm closes the app. The cap is the limit plus 1 GB, and never less than 3 GB. This stops one app from eating your Mac's memory.

We checked these numbers on a real iPhone SE. A test app grew until the phone closed it, and the sim matched. Real apps on the sim read 35 to 70 MB higher than on the phone. The point where the phone first warns can move a lot between runs.

The profile never stops a launch. If it can't load, the app starts as a plain sim app, and `sim_farm_status` says why.

## Android

<!-- ::tool-film id="sim-farm-android" -->

The farm runs Android too. Ask your agent the same way:

```text
Use buoy optimize and the sim farm on our slow feed screen on Android.
```

The agent makes a template from your app's debug `.apk`. The template boots a slim Android phone once. It installs your app and saves the phone. Each copy then starts from that save in about 2 s, with your app on it. A copy throws its changes away when it closes, so every idea starts clean.

- **Phone.** A copy acts like a real Android phone: a 3 GB low-end phone by default. It has that phone's RAM, cores, screen and app memory limit. If Android closes the app for memory, the agent marks that idea out.
- **GPU.** Copies draw with your Mac's GPU, even with no window.
- **Your other tools.** The farm runs its own adb server (port 5137), so `adb kill-server` in another tool can't drop a copy. Android Studio doesn't list them.
- **Room.** A copy uses about 4.3 GB of your Mac's memory, more than a slim iOS sim. `sim_farm_status` says how many more fit.

On our test Mac, a clean phone took 3.4 s instead of 35 s. The app started in 486 ms instead of 1,362 ms. A second phone took 2 s. In Android Studio, you copy the phone, then boot it and add your app. That took over 39 s.

First, get the Android tools once. The farm can use Android Studio's SDK, or download its own (about 1.2 GB). Without Android Studio, this shows Google's license and the line to accept it:

```bash
npx -y @buoy-gg/mcp@latest farm android setup
```

Then you can also run copies yourself:

```bash
npx -y @buoy-gg/mcp@latest farm android template --apk app-debug.apk --reverse 8081:8081
npx -y @buoy-gg/mcp@latest farm android up 2      # two copies in a few seconds
npx -y @buoy-gg/mcp@latest farm android list
npx -y @buoy-gg/mcp@latest farm android down --all
```

`--reverse 8081:8081` links each copy to Metro, so a dev build loads its JS.

## Farm commands

You can run these yourself in a terminal:

```bash
npx -y @buoy-gg/mcp@latest farm status        # sims, memory and what's in use
npx -y @buoy-gg/mcp@latest farm phone list    # phones a sim can act like
npx -y @buoy-gg/mcp@latest farm phone use off # start apps as plain sim apps
npx -y @buoy-gg/mcp@latest farm phone measure # read the iPhone plugged into this Mac
npx -y @buoy-gg/mcp@latest farm doctor        # check Xcode, disk and memory
npx -y @buoy-gg/mcp@latest farm stop          # stop the farm and turn off free sims
```

To close apps at the phone's limit, like a real phone does, run `farm settings phoneOnLimit=kill`. A new phone setting counts the next time the farm starts an app.

## Measure your own phone

`farm phone measure` reads the iPhone plugged into your Mac. It puts a tiny app on the phone for a few seconds. The app reads the phone's memory, cores and app limit. Then the farm removes it and saves the phone. Use it with `farm phone use <id>`.

The phone must be unlocked and trust your Mac. The tiny app needs a paid Apple Developer team. Free Apple accounts can't install it.

## Limits

- Macs only. Android is tested on Apple Silicon Macs.
- Buoy Desktop shows pictures of Android copies, but they have no window to open.
- The farm reads each copy's Buoy id from a debug build. A release build can join Buoy, but the farm can't tell which copy runs it.
- The phone profile doesn't make the app slower. Speed still comes from your Mac.
- Web views in your app run in their own process, and the phone profile doesn't cover them.
- Compare fixes inside one run. Numbers from an older run may not match.

## From an agent

| Tool | What it does |
|---|---|
| `sim_farm_status` | Sims, free memory and disk, what's in use, each app's peak memory |
| `sim_farm_setup` | Makes a template from your `.app` or `.apk`, or adds and removes iOS sims |
| `sim_farm_acquire` | Takes sims for a run, with a goal and a label for each sim. `platform: "android"` takes Android copies |
| `sim_farm_action` | Resets, rebuilds, launches, opens a URL, takes a picture, or installs a new `.apk` on a copy |
| `sim_farm_lock` | Holds the speed-test lock |
| `sim_farm_report` | Sends a short note you can read in Desktop |
| `sim_farm_extend` | Asks for more time on a run |
| `sim_farm_release` | Gives sims back |
| `sim_farm_finish` | Ends a run with a result |

The `buoy-optimize` skill tells the agent when to use each tool. The agent can also take farm sims for other work, like running your tests on many sims.
