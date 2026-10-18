---
title: Perf Monitor
seoTitle: "Perf Monitor for .NET MAUI"
id: maui-tools-perf-monitor
description: "See how fast your MAUI app runs. Record runs and compare them side by side."
---

See how fast your app runs while you use it.
A small panel shows FPS, CPU and memory live.
Record a run, name it and keep it.
Then compare runs to see which one is faster.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyPerfMonitor();
```

Two buttons then show up in Buoy.
PERF turns the live panel on and off.
BENCH opens the tool.

## The live panel

The panel floats over your app.
Tap it to change its layout.
Drag it to move it.
It hides while the BENCH tool is open.
The tool shows its own copy of the panel at the top.

The panel has four numbers.

- JS is how smooth your app's code runs.
- UI is how many frames show each second.
- CPU is how hard your app works the phone.
- MEM is how much memory your app uses.

MAUI runs your code on the UI thread.
So JS and UI often read about the same.
Settings can hide any of the four.

## Record a run

Turn on the panel, then tap Rec.
Use your app the way you want to test.
Tap Rec again to stop.
Give the run a name, then tap Save.

Runs show up in the Single tab.
Tap a run to see its numbers.
Press and hold a run to pick it.
Pick two or more, then tap the chart button to compare them.

## Run a batch

A batch runs each case on its own and records it.
Tap the lightning button to open Automate.
Pick a route, then add a case for each thing you want to test.
Each case can add its own query params to the route.
Tap Run, and Buoy moves through your app for you.

Batches need the Routes tool.
Add `UseBuoyRoutes` and attach your `Shell`.
See [Routes](./routes) for that.

Before each case, Buoy visits a bounce route.
That makes your page load fresh each time.
A batch also runs one extra warmup case first.
The warmup takes the slow first start, and it is left out of the results.

When the batch ends, a report shows one row per case.
The best number in each column is green.
The worst one is red.
Batches show up in the Bulk tab.

Free accounts can run up to 3 cases.
Pro accounts have no limit.

## Mark a moment

Call `BuoyPerfMonitor.Mark` to drop a marker on the run in progress.
Markers show as lines on the run's charts.

```text
BuoyPerfMonitor.Mark("list loaded");
```

## What MAUI leaves out

MAUI has no React, so there are no render counts.
Those switches stay off.
MAUI also can't reload its code between cases.
The bounce route keeps each case fresh instead.

See [all MAUI tools](../tools).
