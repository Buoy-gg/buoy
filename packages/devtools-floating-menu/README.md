<div align="center">

# @buoy-gg/core

The floating developer-tools menu for your React Native app. Add the tool packages you need to inspect requests, app state, storage and performance.

<a href="https://buoy.gg/buoy/latest/docs/quick-start"><b>Quick start</b></a> &nbsp;·&nbsp; <a href="https://buoy.gg">buoy.gg</a> &nbsp;·&nbsp; <a href="https://github.com/Buoy-gg/buoy">All tools</a>

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?style=flat-square&labelColor=10302a&color=2a9d78)](https://www.npmjs.com/package/@buoy-gg/core) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?style=flat-square&labelColor=10302a&color=2a9d78&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)

<a href="https://buoy.gg"><img src="https://raw.githubusercontent.com/Buoy-gg/buoy/main/.github/readme/film.png" alt="Play the 53-second Buoy film on buoy.gg" width="640" /></a>

</div>

Capacitor / Ionic (Beta) uses this package through `/web`.
See the [setup guide](https://buoy.gg/buoy/latest/docs/capacitor) for steps and limits.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/network
npx --package=@buoy-gg/core buoy login
```

Call `Buoy.init` with your key and render `<FloatingDevTools />` inside your existing providers. The login command writes the Expo key to `.env.local`; React Native CLI apps load the key through their own environment config.

The [Quick start](https://buoy.gg/buoy/latest/docs/quick-start) walks through the full setup, and [every tool](https://buoy.gg/buoy/latest/docs/overview) has its own guide.

---

<sub>Part of <a href="https://buoy.gg">Buoy</a>, developer tools that live inside your app. Proprietary software. © Buoy LLC. <a href="https://buoy.gg/terms">Terms</a></sub>
