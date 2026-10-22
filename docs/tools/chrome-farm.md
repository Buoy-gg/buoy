---
title: Chrome Farm
seoTitle: "Chrome Farm — run many slim Chromes on one Mac for web app tests and Buoy Optimize"
id: tools-chrome-farm
description: "Run many slim Chrome instances on one computer. Each one runs your web app with its own storage and its own debugging port. Run tests side by side, and tell your AI agent how many it can trust."
---

The Chrome Farm runs many Chromes on one computer. Each one runs your web app on its own, like a lone Chrome. Your tests or your AI agent can drive every one at the same time.

It's the web version of the [Sim Farm](./sim-farm). It's free with a Buoy account.

## What it's for

- **AI agents.** Your agent starts the Chromes it needs. It keeps them open while it works. With Buoy in your app, each Chrome is its own device for Buoy tools.
- **Tests and speed checks that need care.** Each Chrome has a memory cap. You can pause it. It can also get the computer to itself for a fair speed check.
- **Knowing how many.** We measured how many Chromes a Mac like ours keeps smooth. The farm tells you.

Plain Playwright tests already run side by side. Playwright's own workers use the same slim Chrome. On our test Mac, 64 tests took 18 s with 16 Playwright workers and 16 s with 16 farm Chromes. So for an ordinary test suite, the farm is about even. Use it when you want the things above.

## Start

Get the farm's Chrome once. It's about 100 MB.

```bash
npx -y @buoy-gg/mcp@latest farm chrome setup
```

Start 8 Chromes on your app:

```bash
npx -y @buoy-gg/mcp@latest farm chrome up 8 --url http://localhost:3000
```

Each one prints its debugging address. Playwright can use it:

```js
const browser = await chromium.connectOverCDP(process.env.BUOY_CHROME_CDP);
const page = browser.contexts()[0].pages()[0];
```

Close them when you're done:

```bash
npx -y @buoy-gg/mcp@latest farm chrome down --all
```

## Run your tests side by side

`farm chrome run` starts the Chromes, runs your command once in each, then closes them all.

```bash
npx -y @buoy-gg/mcp@latest farm chrome run 8 --url http://localhost:3000 -- node my-test.mjs
```

Each run of your command gets these:

| Name | What it is |
|---|---|
| `BUOY_CHROME_CDP` | The browser's address, for Playwright's `connectOverCDP` |
| `BUOY_CHROME_PAGE_WS` | The page's address, for Puppeteer or raw CDP |
| `BUOY_CHROME_INDEX` | Which Chrome this is, from 0 |
| `BUOY_CHROME_URL` | Your app's address |

Got a list of jobs? Put one per line in a file and pass `--jobs`. The Chromes take jobs in turn. Between jobs, the farm clears what the last job left: storage, cookies, cache and extra tabs. Then it loads the app again in the same Chrome. That's about 4 times faster than starting a new Chrome. The job's line goes last on your command. It's also in `BUOY_JOB`. If a Chrome stops working, its jobs go to the others.

```bash
npx -y @buoy-gg/mcp@latest farm chrome run 12 --url http://localhost:3000 --jobs jobs.txt -- node my-test.mjs
```

At the end it prints how many passed and failed. A failed job shows the end of its output.

On our test Mac, 64 tests (5 burger orders each) took 130 s on one Chrome and 23 s on 16 Chromes. Each test started its own Node and Playwright, which costs about half a second of CPU. A test runner that keeps one process per worker does better.

### Playwright Test

Start the Chromes, then give each Playwright worker its own one:

```bash
npx -y @buoy-gg/mcp@latest farm chrome up 16 --url http://localhost:3000
npx -y @buoy-gg/mcp@latest farm chrome list --json   # each Chrome's browserWs
```

```js
// farm.fixture.mjs: worker i drives farm Chrome i, and the app resets between tests.
import { test as base, chromium } from "@playwright/test";
const endpoints = process.env.BUOY_CHROME_ENDPOINTS.split(","); // the browserWs list, comma-separated

export const test = base.extend({
  farmBrowser: [async ({}, use, workerInfo) => {
    const browser = await chromium.connectOverCDP(endpoints[workerInfo.parallelIndex % endpoints.length]);
    await use(browser);
    await browser.close(); // disconnects; the Chrome stays
  }, { scope: "worker" }],
  page: async ({ farmBrowser }, use) => {
    const page = farmBrowser.contexts()[0].pages()[0];
    const cdp = await page.context().newCDPSession(page);
    const url = new URL(page.url());
    await cdp.send("Storage.clearDataForOrigin", { origin: url.origin, storageTypes: "all" });
    await page.goto(url.href);
    await use(page);
  },
});
```

Then run `npx playwright test --workers 16`. On our test Mac, 64 tests took 112 s on one Chrome and 16 s on 16.

## Why the farm's Chrome is slim

The farm runs `chrome-headless-shell` from Chrome for Testing, at one fixed version. It's Chrome's own engine without the browser window parts. On our test Mac, it used a quarter of full Chrome's memory with the app open:

| | Full Chrome, window open | Farm Chrome |
|---|---|---|
| Memory with the app open | 918 to 932 MB | 227 to 243 MB |
| Memory while the app is busy | 1,420 MB | 541 to 601 MB |
| Programs | 7 | 4 |
| Ready to use | 0.6 to 1.3 s | about 0.4 s |

On a Mac, each Chrome draws with the Mac's GPU, like a lone Chrome. Without the GPU, Chrome draws on the CPU. Then pages with lots of WebGL or canvas run far slower. On our test Mac, a WebGL page with 20,000 lights ran at 11 fps on the CPU. It ran at 60 fps on the GPU. Our test app used the same memory either way. To draw on the CPU anyway, add `--no-gpu`. Don't compare speed that way.

Every Chrome gets its own profile, so storage and cookies never mix. A fixed version means no surprise updates and no Chrome experiments that change results between runs.

## How many to run

The farm can hold far more Chromes open than your computer can keep busy. Memory is rarely the limit. The limit is how many of them work hard at the same moment.

We tested on an M5 Max: 18 cores (6 "Super" and 12 "Performance") and 128 GB. Every Chrome clicked through our test app nonstop, with no pauses:

| Chromes busy at once | Each step, vs one alone | Smooth frames | Work done, vs one alone |
|---|---|---|---|
| 1 | same | yes | 1x |
| 6 | 13% slower | yes | 5.9x |
| 12 | 27% slower | yes | 11.3x |
| 16 | 34% slower | yes | 14.8x |
| 24 | 2.1 times slower | yes | 17x |

Up to 16, every test passed and every page drew smooth frames with no input lag. Steps got slower for one reason. A Super core runs the app's code about 1.6 times faster than a Performance core. One Chrome alone always gets a Super core. A crowd shares 6 of them.

Real tests pause between steps to wait and check things. So we ran it again with a 300 ms pause after each step:

| Chromes at once | Each step, vs one alone | All passed, smooth frames | Work done, vs one alone |
|---|---|---|---|
| 8 | same | yes | 8x |
| 16 | same | yes | 16x |
| 24 | 4% slower | yes | 24x |
| 32 | 49% slower | yes | 32x |
| 48 | 2.9 times slower | yes | 37x |

With pauses, 24 ran at a lone Chrome's speed and 32 still did 32 times the work. `farm chrome doctor` checks your computer and suggests numbers. If your tests have tight time limits, raise them when you run many at once.

We also tried full Chrome windows, one per test, on the same Mac. Full Chrome dropped frames at 12. It kept a lone Chrome's speed with only 1 busy. The farm stayed smooth to 16 and kept lone speed up to 6.

### 100 at once

We opened 100 Chromes on the test app. All 100 were ready in 55 seconds, starting 8 at a time. Together they used 31.5 GB. Closing all of them took 2.6 seconds.

An open app can still do work when no test touches it. Ours has a countdown and Buoy inside, so 100 idle copies kept 13 cores busy. Pause the ones you aren't using:

```bash
npx -y @buoy-gg/mcp@latest farm chrome pause --all
npx -y @buoy-gg/mcp@latest farm chrome resume <id>
```

A paused Chrome keeps its page and its memory but uses no CPU. It wakes right away.

## Fair speed numbers

Frame times and input delay match a lone Chrome only up to a point. That point is the number of top cores you have. On the M5 Max that's 6.

When more are busy, take a quiet window. It pauses every other Chrome for a few seconds, so one gets the computer to itself:

```bash
npx -y @buoy-gg/mcp@latest farm chrome quiet <id> --seconds 10
```

Paused Chromes run no code, timers or frames. They wake up by themselves. In our test, one Chrome took 124 ms per step with 11 busy neighbors. It took 54 ms alone and 46 ms in a quiet window. Then `farm chrome list` shows how much of its work ran on the fastest cores.

## Boost

With `--boost`, the Chrome a test is driving right now gets one of the computer's fastest cores. The others sit a little lower until their next step. Only scheduling priority changes, so timers and frames behave as before.

```bash
npx -y @buoy-gg/mcp@latest farm chrome up 32 --url http://localhost:3000 --boost
```

It helps when tests pause between steps, like most real tests. On our test Mac, with 32 to 48 Chromes pausing between steps, steps took 6% to 24% less time with boost. It helped more when the Mac was busier. There's a cost: with 48 Chromes, the slowest Chrome's input delay went from about 55 ms to about 80 ms. Boost doesn't help tests that click nonstop, since then every Chrome is mid-step. It needs a Mac with Xcode's command line tools. The farm builds a tiny helper the first time.

## Caps

One Chrome can't take down the rest.

- **Memory cap.** Each Chrome has a cap, 2 GB by default. A Chrome that goes over it is closed. Set it with `--cap-mb`.
- **Hangs.** The farm checks every Chrome every 15 seconds and notes the ones that stop answering.
- **Own process.** Each Chrome runs alone. A crash only ends that one.

## Your app's own calls

Each Chrome is a full copy of your app. If your app calls a server when it starts, 50 Chromes call it 50 times. Point your app at a test server, or block calls you don't want repeated.

## Farm commands

```bash
npx -y @buoy-gg/mcp@latest farm chrome setup            # get the farm's Chrome
npx -y @buoy-gg/mcp@latest farm chrome up 8 --url <app> # start 8
npx -y @buoy-gg/mcp@latest farm chrome list             # memory, CPU and addresses
npx -y @buoy-gg/mcp@latest farm chrome reset --all      # clear storage, reload the app
npx -y @buoy-gg/mcp@latest farm chrome pause --all      # keep pages, use no CPU
npx -y @buoy-gg/mcp@latest farm chrome resume --all     # wake them
npx -y @buoy-gg/mcp@latest farm chrome shot <id> a.png  # save a screenshot
npx -y @buoy-gg/mcp@latest farm chrome down --all       # close them
npx -y @buoy-gg/mcp@latest farm chrome doctor           # cores, memory, suggested counts
npx -y @buoy-gg/mcp@latest farm chrome stop             # close all and stop the farm
```

Add `--desktop` for a 1280 × 800 window. The default is a phone: 402 × 874 at 3x, with touch.

## From an agent

| Tool | What it does |
|---|---|
| `chrome_farm_status` | Running Chromes, their memory and CPU, and suggested counts |
| `chrome_farm_up` | Starts Chromes on your app and returns their addresses |
| `chrome_farm_reset` | Clears storage and reloads the app |
| `chrome_farm_pause` | Pauses or wakes Chromes you aren't using |
| `chrome_farm_quiet` | Pauses the others for a fair speed sample |
| `chrome_farm_screenshot` | A picture of one Chrome |
| `chrome_farm_down` | Closes Chromes |

If your app has Buoy inside, each Chrome also shows up in `list_devices` as its own web device. Then every Buoy tool works on it.

An agent only touches the Chromes it started. Its quiet windows, `all: true` and pauses never reach another session's Chromes. From the command line, `farm chrome quiet` pauses every other Chrome.

## Limits

- Mac first. Linux and Windows are not tested yet. There the farm doesn't pick the GPU yet. Quiet windows and pausing use Unix signals, so Windows needs more work.
- Chromes run without a window. You see them through screenshots.
- Speed numbers come from your computer. Compare ideas inside one run.
