---
title: Solid (Beta)
seoTitle: "Buoy for Solid (Beta)"
id: web-solid
description: "Add Buoy to a Solid app. Pick tools, sign in, and open your app."
---

Beta. Buoy runs in Solid 1.9 apps built with Vite.
Solid 2 is not supported yet.

## Start here

Let your coding agent install Buoy:

<!-- ::agent-install platform="solid" where="docs-solid" -->

To do it by hand, use these four steps.

### 1. Add Buoy

Run this in your app folder:

<!-- ::PM npm="npm install --save-dev @buoy-gg/core @buoy-gg/solid @buoy-gg/network" yarn="yarn add --dev @buoy-gg/core @buoy-gg/solid @buoy-gg/network" pnpm="pnpm add -D @buoy-gg/core @buoy-gg/solid @buoy-gg/network" bun="bun add --dev @buoy-gg/core @buoy-gg/solid @buoy-gg/network" -->

```sh
npm install --save-dev @buoy-gg/core @buoy-gg/solid @buoy-gg/network
```

Add more tools the same way, such as `@buoy-gg/storage`.
Keep all Buoy packages on the same release.
Buoy brings its own React for its own screens.
Your app stays all Solid.

### 2. Add the Vite plugin

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import solid from 'vite-plugin-solid';
import { buoy } from '@buoy-gg/core/vite';

export default defineConfig({
  plugins: [solid(), buoy()],
});
```

The plugin finds the tools you installed.
It adds Buoy's early hook to `index.html`.
It links Solid Router and Solid Query when you have them.
It leaves Buoy out of prod builds.

### 3. Add the Buoy component

Put it in your router's `root`, inside `QueryClientProvider`:

```tsx
import { Router, Route } from '@solidjs/router';
import { QueryClientProvider } from '@tanstack/solid-query';
import { Buoy } from '@buoy-gg/solid';

function Layout(props) {
  return (
    <>
      {props.children}
      <Buoy />
    </>
  );
}

render(() => (
  <QueryClientProvider client={queryClient}>
    <Router root={Layout}>
      <Route path="/" component={Home} />
    </Router>
  </QueryClientProvider>
), document.getElementById('root')!);
```

`<Buoy />` shows nothing in your page.
It starts Buoy in dev builds, after the first render.
It stops Buoy when it goes away, so hot reload is safe.
You can put it outside the router too.
Then Routes shows plain URLs only.

### 4. Sign in

```sh
npx buoy login
```

Sign in with a Free or Pro account.
The CLI writes `VITE_BUOY_KEY` to a file that Git ignores.
`<Buoy />` reads it for you, so you add no key to your code.

Run your app with `npm run dev`.
Open Buoy's dial, then open Network.
Make a web call in your app and check the list.

## What works

| Tool | In Solid |
|---|---|
| Network, Storage, Console | Yes. They watch the browser itself. |
| Routes | Yes. Inside `<Router>` it shows route names and params. |
| Query | Yes, with Solid Query. See below. |
| Zustand | Yes, for vanilla Zustand stores and Solid stores. |
| Highlight | Yes. It boxes the parts that changed. |
| Redux | Yes, for Redux Toolkit. Load `@buoy-gg/redux/web` before you make the store. |
| Jotai | Yes, for `jotai/vanilla` atoms. Pass them to `watchAtoms`. |
| Clock, Lifecycle, Location, Permissions | Yes. They work in a plain browser. |
| Env, Events, Perf Monitor, Images, Impersonate, Time Machine | Yes. |

## Routes

Inside `<Router>`, Routes shows each route's name and params.
Each page change shows up once.
Name a route with `info`:

```tsx
<Route path="/pokemon/:id" component={Detail} info={{ name: 'Pokémon' }} />
```

To list every page in the sitemap, pass your routes:

```tsx
<Buoy routes={routes} />
```

Without it, pages join the list as you visit them.
The Go button and the MCP `navigate` tool use Solid Router.

## Solid Query

Install the Query tool and React Query too:

```sh
npm install --save-dev @buoy-gg/react-query @tanstack/react-query
```

Use the same version as your `@tanstack/solid-query`.
Both must share one copy of `@tanstack/query-core`.
`<Buoy />` finds your client from `QueryClientProvider`.

## Solid stores and signals

Pass them to Buoy's Zustand tool:

```tsx
import { buoySolidSignal, buoySolidStore } from '@buoy-gg/solid/store';

<Buoy zustandStores={{
  todos: buoySolidStore([todos, setTodos]),
  count: buoySolidSignal([count, setCount]),
}} />
```

You can read and edit them in the tool.
An edit keeps the fields you did not change.
A signal shows up as `{ value }`.
Vanilla Zustand stores go in the same list as they are.

## Highlight

Highlight boxes the parts of your page that changed.
It names the nearest component you wrote.
It skips Solid's own `Show`, `For` and `Switch`.
It reads Solid's dev hooks, so it works in dev builds only.
It only wraps your app's code while Highlight is on.

It works next to solid-devtools.
Buoy keeps the dev hooks solid-devtools sets, in either order.

## Keep your settings out of prod

Put app settings for Buoy in their own file.
Load it with `load`, behind a dev check:

```tsx
const load = import.meta.env.DEV
  ? () => import('./buoy-settings').then((m) => m.settings)
  : undefined;

<Buoy load={load} />
```

The dev check lets the prod build drop that file.

## QA builds

To keep Buoy in a QA build, set both of these:

```ts
buoy({ enableInRelease: true })
```

```tsx
<Buoy enableInRelease />
```

Account and plan rules still apply.

## Without the Vite plugin

`<Buoy />` still runs, but it loads no tools by itself.
Pass them with `modules`, and pass `queryClient` too.
Put this line first in your entry file:

```ts
import '@buoy-gg/solid/register';
```

Routes then shows plain URLs only.
Your build must also drop Buoy from prod.
Resolve the `buoy-disabled` export condition there.

## SolidStart

Buoy runs in SolidStart 2.
Add `buoy()` after `solidStart()` in your Vite config.
SolidStart has no `index.html`, so the plugin can't add the hook.
Put the hook first in `entry-client.tsx`.
Load tools like Redux before the client:

```tsx
// src/entry-client.tsx
import '@buoy-gg/solid/register';

if (import.meta.env.DEV) {
  await import('@buoy-gg/redux/web');
}

const { mount, StartClient } = await import('@solidjs/start/client');
mount(() => <StartClient />, document.getElementById('app')!);
```

Import the client last.
It loads your app, and your app makes the store.
If the client loads first, Redux can't see the store.

## TypeScript

The types in `@buoy-gg/solid` need no React types.
Your app type-checks as it is.
Code that imports `@buoy-gg/core` or a tool's `/web` entry is different.
Those types name React types your app does not have.
For that code, set `skipLibCheck: true` in `tsconfig.json`.
