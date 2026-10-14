<div align="center">

# @buoy-gg/redux

Inspect your connected Redux store, review captured actions and state changes, and dispatch actions from your device.

<a href="https://buoy.gg/buoy/latest/docs/tools/redux"><b>Redux guide</b></a> &nbsp;·&nbsp; <a href="https://buoy.gg">buoy.gg</a> &nbsp;·&nbsp; <a href="https://github.com/Buoy-gg/buoy">All tools</a>

[![npm version](https://img.shields.io/npm/v/@buoy-gg/redux?style=flat-square&labelColor=10302a&color=2a9d78)](https://www.npmjs.com/package/@buoy-gg/redux) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/redux?style=flat-square&labelColor=10302a&color=2a9d78&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/redux)

<a href="https://buoy.gg/buoy/latest/docs/tools/redux"><img src="https://raw.githubusercontent.com/Buoy-gg/buoy/main/.github/readme/tools/redux.png" alt="Watch the Redux film on buoy.gg" width="640" /></a>

</div>

## Install

```bash
npm install @buoy-gg/core @buoy-gg/redux
```

Import `@buoy-gg/redux` before the module that creates your store, and keep Redux DevTools integration enabled. Don't create a second store for Buoy.

The [Redux guide](https://buoy.gg/buoy/latest/docs/tools/redux) covers setup, what it captures and its limits. New to Buoy? Start with the [Quick start](https://buoy.gg/buoy/latest/docs/quick-start).

---

<sub>Part of <a href="https://buoy.gg">Buoy</a>, developer tools that live inside your app. Proprietary software. © Buoy LLC. <a href="https://buoy.gg/terms">Terms</a></sub>

### Angular stores

NgRx apps can use `provideBuoyNgRx` from `@buoy-gg/angular/ngrx`.
It gives NgRx its own DevTools object through DI.
It never claims the browser's Redux extension global.
NGXS 22 apps use `provideBuoyNgxs` from `@buoy-gg/angular/ngxs`.
Use just one bridge for the Redux tool per app.
See the [Angular setup](https://buoy.gg/buoy/latest/docs/web/angular) for each one.

A store bridge can call `connectReduxAdapter(store)`.
The store must read state and dispatch actions.
It must also handle `@@buoy/JUMP_TO_STATE` with a state payload.
Call the returned stop function when the adapter stops.
