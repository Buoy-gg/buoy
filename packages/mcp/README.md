<div align="center">

# @buoy-gg/mcp

Connect an MCP client to supported Buoy tools in a running app. Read captured state, invoke tool actions, and run configured benchmarks.

<a href="https://buoy.gg/buoy/latest/docs/mcp"><b>MCP guide</b></a> &nbsp;·&nbsp; <a href="https://buoy.gg">buoy.gg</a> &nbsp;·&nbsp; <a href="https://github.com/Buoy-gg/buoy">All tools</a>

[![npm version](https://img.shields.io/npm/v/@buoy-gg/mcp?style=flat-square&labelColor=10302a&color=2a9d78)](https://www.npmjs.com/package/@buoy-gg/mcp) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/mcp?style=flat-square&labelColor=10302a&color=2a9d78&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/mcp)

<a href="https://buoy.gg/buoy/latest/docs/mcp"><img src="https://raw.githubusercontent.com/Buoy-gg/buoy/main/.github/readme/tools/optimize.png" alt="Watch the buoy-optimize film on buoy.gg" width="640" /></a>

</div>

## Install

```bash
npx -y @buoy-gg/mcp@latest init
```

Run it from your app's directory. It registers a `buoy` server in your MCP configs and adds the `buoy-optimize` skill. Your React Native app also needs `@buoy-gg/external-sync`. A free account gets the basic tools, and Pro adds the full MCP. Restart your editor, open your app, and start with `list_devices`.

The [MCP guide](https://buoy.gg/buoy/latest/docs/mcp) covers setup, what it captures and its limits. New to Buoy? Start with the [Quick start](https://buoy.gg/buoy/latest/docs/quick-start).


## Read screens

Use `native_inspect` to read a point without a tap.
Pass `pointX` and `pointY` in iOS Sim points.
Other hosts use pixels. On a real iPhone, this reads the app view.
The `x` and `y` fields still use screen fractions.

Use `screenshot_component` with `compareTo` to view a saved PNG
next to the live screen. The saved file comes first.
Use a full path, or start at the project root.
The saved file stays as it is.

## Do more in one call

`run_flow` can tap, type and swipe in one call.
A `swipe` step with `untilEnd: true` goes to the end of a list.
Pass `real: true` to make each tap a real touch.
`screen_recording` can take `steps`. It records them, then stops and saves.

---

<sub>Part of <a href="https://buoy.gg">Buoy</a>, developer tools that live inside your app. Proprietary software. © Buoy LLC. <a href="https://buoy.gg/terms">Terms</a></sub>
