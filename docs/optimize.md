---
title: Buoy Optimize
seoTitle: "Buoy Optimize — make a slow React Native screen faster with your AI agent"
id: optimize
description: "Tell your coding agent what's slow. Buoy Optimize builds each idea as its own version, benchmarks every version on the device with Bench, and keeps what the measurements and your eyes agree on."
---

Buoy Optimize is a skill for your coding agent. You tell the agent which screen is slow. It builds each idea for a fix as its own version of that screen, runs every version on the device with [Bench](./tools/perf-monitor), and ranks them from the measurements. You check that each version still looks right. The agent keeps what's faster and correct, then starts the next round.

<!-- ::tool-film id="optimize" -->

## How a round works

1. You describe the problem, for example "the light preview is slow with a lot of lights" or "this list drops frames when I scroll". Say "buoy optimize" to start the skill.
2. The agent keeps the current code as the baseline and builds each idea as a variant. Variants live on a development test route and are picked by a route parameter, so you can open any of them in the app.
3. Bench runs every variant on the connected device, several times each. It runs a throwaway warmup case first, shuffles the case order and waits between runs, so a warm phone or one lucky run is less likely to decide the ranking.
4. You open each variant and look at it. A variant that's faster but draws the wrong thing is out, whatever its numbers say. The agent can take screenshots, but you decide what looks right.
5. The agent keeps the variants that helped, drops the rest, combines ideas that work together and runs the next batch on the same workload.

It stops when the screen is fast enough, when the time budget you gave it runs out, or when the next step needs hardware or a decision from you. The final report names the chosen version, the device and build it was measured on, the settings, and what's still unconfirmed.

## Simulator first, then a real phone

Rounds on a simulator are quick and good for ruling ideas out. Simulator numbers only describe the simulator, though, so confirm the finalists on the phone your users have.

A phone gets warmer over a long batch, and a warm phone runs slower. Bench's Fast preset uses short cooldowns and assumes the phone stays cool, which is what a cold pack or cooling pad under the phone is for. The Slow preset waits 20 seconds between runs so a room-temperature phone can cool down on its own. Shuffling the case order is on in both, so any heat that's left spreads across every variant instead of piling up on the last one.

## What you need

- [Bench](./tools/perf-monitor) (`@buoy-gg/perf-monitor`) in a React Native development build. Bench's native dependencies don't run in Expo Go.
- The [Buoy MCP server](./mcp) in your editor. `npx -y @buoy-gg/mcp@latest init` sets it up and installs the `buoy-optimize` skill. Rerunning it overwrites the skill files, so save any changes you made to them first.
- Buoy Pro. Running benchmarks over MCP is a Pro feature.

The agent drives Bench with these MCP tools: `get_benchmark_settings`, `run_benchmark_batch`, `get_batch_report` and `compare_reports`. For a slow interaction rather than a slow screen, it starts with `measure_renders`, which counts renders during a set of steps and can compare them before and after a change.

## An example: 28 lights to 12,000

The film follows the light preview in EverLights, a Christmas light app. The first preview drew 28 lights. A real house needs thousands.

In the 12,000-light round, the agent tried three ways of producing each frame. Bench ran each one three times on an iPhone simulator:

| Version | UI FPS | JS FPS | CPU |
| --- | --- | --- | --- |
| The original 28-light preview | 60 | 59.2 | 68.6% |
| The current code at 12,000 lights | 49.1 | 8.9 | 115.7% |
| The chase moved to the UI thread, 12,000 lights | 47.5 | 47.1 | 22.8% |

The winning version stopped recomputing every light on the JavaScript thread. The chase effect only shifts colors along the strip, so the new version computes the colors once and moves them on the UI thread. JS FPS went from 8.9 to 47.1 and CPU dropped by about four fifths. UI FPS stayed in the high 40s, so this round didn't fix drawing speed, and the numbers still need confirming on a phone.

## What's next

- [Bench](./tools/perf-monitor): the benchmark runner Buoy Optimize drives
- [AI / MCP Server](./mcp): set up the server and the skill
- [Render Highlighter](./tools/highlight-updates): see which components re-render

## FAQ

### How do I make a slow React Native screen faster with AI?

Set up the [Buoy MCP server](./mcp), install [Bench](./tools/perf-monitor) in a development build, and ask your coding agent to "buoy optimize" the screen. The agent builds candidate fixes as separate variants, measures each on the device, and asks you to check how they look before it keeps one.

### Which coding agents work with Buoy Optimize?

Any agent that can use an MCP server and read a skill file, such as Claude Code or Cursor. `init` writes the MCP config for Claude Code, Cursor and VS Code.

### Can I trust a benchmark from the simulator?

Only for the simulator. Use simulator rounds to rule ideas out quickly, then run the finalists on a real phone before you ship.

### Does Buoy Optimize work with Flutter?

Not yet. The skill and `run_benchmark_batch` are React Native only.
