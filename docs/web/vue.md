---
title: Vue and Nuxt (Beta)
seoTitle: "Buoy for Vue and Nuxt (Beta)"
id: web-vue
description: "Add Buoy to a Vue or Nuxt app. Pick tools, sign in, and open your app."
---

Beta. Buoy runs in Vue 3.3 and up, built with Vite.
It runs in Nuxt 4 too.
Buoy brings its own React for its own screens.
Your app stays all Vue.

## Start here

Let your coding agent install Buoy:

<!-- ::agent-install platform="vue" where="docs-vue" -->

To do it by hand, use these four steps.
For Nuxt, skip to [Nuxt](#nuxt).

### 1. Add Buoy

Run this in your app folder:

<!-- ::PM npm="npm install --save-dev @buoy-gg/core @buoy-gg/vue @buoy-gg/network" yarn="yarn add --dev @buoy-gg/core @buoy-gg/vue @buoy-gg/network" pnpm="pnpm add -D @buoy-gg/core @buoy-gg/vue @buoy-gg/network" bun="bun add --dev @buoy-gg/core @buoy-gg/vue @buoy-gg/network" -->

```sh
npm install --save-dev @buoy-gg/core @buoy-gg/vue @buoy-gg/network
```

Add more tools the same way, such as `@buoy-gg/storage`.
Keep all Buoy packages on the same release.

### 2. Add the Vite plugin

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import { buoy } from '@buoy-gg/core/vite';

export default defineConfig({
  plugins: [vue(), buoy()],
});
```

The plugin finds the tools you installed.
It adds Buoy's early hook to `index.html`.
It leaves Buoy out of prod builds.

### 3. Add the Buoy plugin

```ts
// src/main.ts
import { createApp } from 'vue';
import { buoy } from '@buoy-gg/vue';

createApp(App)
  .use(pinia)
  .use(router)
  .use(VueQueryPlugin, { queryClient })
  .use(buoy)
  .mount('#app');
```

It can go anywhere in the `use` chain.
When your app mounts, it finds your router, Pinia and Vue Query client.
It starts Buoy in dev builds.
It stops Buoy when your app unmounts.
It adds nothing to your page.

### 4. Sign in

```sh
npx --package=@buoy-gg/core buoy login
```

Sign in with a Free or Pro account.
The CLI writes `VITE_BUOY_KEY` to a file that Git ignores.
The Buoy plugin reads it for you, so you add no key to your code.

Run your app with `npm run dev`.
Open Buoy's dial, then open Network.
Make a web call in your app and check the list.

## What works

| Tool | In Vue |
|---|---|
| Network, Storage, Console | Yes. They watch the browser itself. |
| Routes | Yes. It shows route names and params from Vue Router. |
| Query | Yes, with Vue Query. See below. |
| Zustand | Yes, for Pinia stores, Vue refs and vanilla Zustand stores. |
| Highlight | Yes. It boxes the components that rendered. |
| Redux | Yes, for Redux Toolkit. Load `@buoy-gg/redux/web` before you make the store. |
| Jotai | Yes, for `jotai/vanilla` atoms. Pass them to `watchAtoms`. |
| Clock, Lifecycle, Location, Permissions | Yes. They work in a plain browser. |
| Env, Events, Perf Monitor, Images, Impersonate, Time Machine | Yes. |

## Routes

Routes shows each page's name and params.
Each page change shows up once, the first page too.
The name is the route's `name`:

```ts
{ path: '/pokemon/:id', name: 'Pokémon', component: Detail }
```

A route with no name shows its path.
The sitemap lists every route your router has.
It updates when you add or remove routes.
The Go button and the MCP `navigate` tool use Vue Router.

## Vue Query

Install the Query tool and React Query too:

```sh
npm install --save-dev @buoy-gg/react-query @tanstack/react-query
```

Use the same version as your `@tanstack/vue-query`.
Both must share one copy of `@tanstack/query-core`.
The Buoy plugin finds your client from `VueQueryPlugin`.
A custom `queryClientKey` works too.

## Pinia

Your Pinia stores show up in Buoy's Zustand tool by themselves.
Each one goes by its id.
A store shows up the first time your app uses it.
You can read and edit a store in the tool.
An edit keeps the fields you did not change.
Getters and actions are not shown.

To show a plain `ref` or `reactive`, pass it to the tool:

```ts
import { buoyVueRef } from '@buoy-gg/vue/pinia';

app.use(buoy, {
  zustandStores: { count: buoyVueRef(count) },
});
```

A `ref` that holds one value shows up as `{ value }`.
Vanilla Zustand stores go in the same list as they are.

## Highlight

Highlight boxes the components that rendered.
It names each one by its component name.
It skips Vue's own `Transition`, `KeepAlive`, `Suspense` and `Teleport`.
It reads Vue's dev hook, so it works in dev builds.

It works next to Vue DevTools.
That's the browser extension, `vite-plugin-vue-devtools` and Nuxt DevTools.
Buoy shares Vue's dev hook with them, in any order.

Vue 3.6's Vapor components get no boxes yet.
Your other components still do.

## Keep your settings out of prod

Put app settings for Buoy in their own file.
Load it with `load`, behind a dev check:

```ts
app.use(buoy, {
  load: import.meta.env.DEV
    ? () => import('./buoy-settings').then((m) => m.settings)
    : undefined,
});
```

The dev check lets the prod build drop that file.

## QA builds

To keep Buoy in a QA build, set both of these:

```ts
buoy({ enableInRelease: true })
```

```ts
app.use(buoy, { enableInRelease: true })
```

The Vite plugin also keeps Vue's dev hook in that build, so Highlight still works.
Account and plan rules still apply.

## Nuxt

Install Buoy the same way, then add the module:

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@buoy-gg/vue/nuxt'],
});
```

That's all.
You need no Vite plugin and no `app.use`.
The module adds Buoy's early hook to Nuxt's browser entry.
It starts Buoy in the browser, after Nuxt makes your app.
The server build gets none of Buoy.
Neither does a prod build.

Pass options with the `buoy` key:

```ts
export default defineNuxtConfig({
  modules: ['@buoy-gg/vue/nuxt'],
  buoy: { options: { environment: 'qa' } },
});
```

These options must be plain data.
For options with functions, such as `load`, set `setup: false`.
Then start Buoy in your own client plugin:

```ts
// nuxt.config.ts
buoy: { setup: false },
```

```ts
// app/plugins/buoy.client.ts
import { buoy } from '@buoy-gg/vue';

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.use(buoy, { load });
});
```

`@pinia/nuxt` stores show up by themselves.
Route names come from your page names.
Nuxt names a page after its file, such as `index`.

Network shows the calls your app makes in the browser.
A `useFetch` that runs on the server is not in the list.
The browser gets its answer inside the page.

For a QA build, set `buoy: { enableInRelease: true }`.

## Without the Vite plugin

The Buoy plugin still runs, but it loads no tools by itself.
Pass them with `modules`.
Put this line first in your entry file, before `createApp`:

```ts
import '@buoy-gg/vue/register';
```

Your build must also drop Buoy from prod.
Resolve the `buoy-disabled` export condition there.

## TypeScript

The types in `@buoy-gg/vue` need no React types.
Code that imports `@buoy-gg/core` or a tool's `/web` entry is different.
Those types name React types your app does not have.
Vue's own starter sets `skipLibCheck: true`, so it type-checks as it is.
If yours doesn't, set it in `tsconfig.json`.
