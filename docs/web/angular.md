---
title: Angular (Beta)
seoTitle: "Buoy for Angular (Beta)"
id: web-angular
description: "Add Buoy to Angular. Pick tools, sign in, and open your app."
---
<!-- ::tool-film id="angular" -->

Beta. Buoy runs in Angular 19 to 22.

## Start here

Let your coding agent install Buoy:

<!-- ::agent-install platform="angular" where="docs-angular" -->

To do it by hand, use these three steps.

### 1. Add Buoy

Run this in your Angular app folder:

<!-- ::PM npm="ng add @buoy-gg/angular" yarn="ng add @buoy-gg/angular" pnpm="ng add @buoy-gg/angular" bun="ng add @buoy-gg/angular" -->

```sh
ng add @buoy-gg/angular
```

Pick the tools you want from the list.
In a workspace, add `--project <app-name>`.
Keep all Buoy packages on the same release.

### 2. Sign in

```sh
npx buoy login
```

Sign in with a Free or Pro account.
In a workspace, add `--project <app-name>` here too.
The CLI writes a key file that Git ignores.
The builder reads it. Do not paste keys in code.

### 3. Run your app

```sh
ng serve
```

Open Buoy's dial, then open Network.
Use your app to make a web call.
Check that the call shows up in the list.

## What ng add changed

It keeps your app settings and adds two builders:

```json
"build": { "builder": "@buoy-gg/angular:application" },
"serve": { "builder": "@buoy-gg/angular:dev-server" }
```

It adds this call last in your app's providers:

```ts
import { provideBuoy } from '@buoy-gg/angular';

providers: [
  // Your app's providers stay here.
  provideBuoy(),
],
```

The builder finds your tools and adds the early hook.
It links Router, Query, and Highlight when they are installed.
You need no tool list for this call.
Buoy brings its own React deps for its UI.
Prod keeps only a small, empty provider.
Buoy's tools, React DOM, and React Native Web stay out.

Options add to auto setup. Your values win.
A plain `provideBuoy()` call needs no dev guard.
If your options use `import()`, put a dev guard inside the loader.
Without that guard, the code stays in prod.
Use `zustandStores` for your app's vanilla Zustand stores.
A manual `modules` map turns auto setup off.
See the [package README](https://www.npmjs.com/package/@buoy-gg/angular#app-options) for merge rules and app hooks.

## Add a tool later

Install the tool, then restart `ng serve`:

<!-- ::PM npm="npm install --save-dev @buoy-gg/console" yarn="yarn add --dev @buoy-gg/console" pnpm="pnpm add -D @buoy-gg/console" bun="bun add --dev @buoy-gg/console" -->

```sh
npm install --save-dev @buoy-gg/console
```

The tool shows up with no new import.
For Query, `ng add` also adds `@tanstack/react-query`.
Match it to your Angular Query version.
Both must share one `@tanstack/query-core` version and copy.

## NgRx and NGXS

Keep your normal NgRx store and effects.
Select Redux when you run `ng add`.
Keep NgRx DevTools in a dev guard. Put Buoy last:

```ts
import { provideStoreDevtools } from '@ngrx/store-devtools';
import { provideBuoy } from '@buoy-gg/angular';

declare const ngDevMode: boolean | undefined;

// At the end of providers:
(typeof ngDevMode === 'undefined' || ngDevMode)
  ? provideStoreDevtools({ maxAge: 50 })
  : [],
provideBuoy(),
```

Buoy links NgRx through DI. It leaves the browser global alone.
If another provider wins, Buoy warns: move `provideBuoy()` last.
The tool shows actions, state, and diffs.
Replay sends the action again. Jump restores saved state.

NGXS needs its adapter too. Keep it in a dev guard:

```ts
import { provideBuoyNgxs } from '@buoy-gg/angular/ngxs';

// At the end of providers:
(typeof ngDevMode === 'undefined' || ngDevMode) ? provideBuoyNgxs() : [],
provideBuoy(),
```

It uses `NGXS_PLUGINS`. Do not mix both store adapters.
SignalStore and a Signals view are not shipped yet.

## QA builds

To keep Buoy in a QA build, set this build option:

```json
"buoy": { "release": true }
```

This skips the prod swap. Account and plan rules still apply.
Free dev keys stay out of prod code.
Desktop sync also needs `externalSync: { enableInRelease: true }`.
NgRx, NGXS, and Highlight still need Angular dev mode.

## Angular 19

The builders work with CLI 19, 20, 21, and 22.
CLI 19 needs no `conditions` setting with Buoy's builder.
Highlight needs Angular 20 or newer and the dev hook.
It counts view checks, not paints. It leaves other hooks alone.
The hook is private Angular API, so check Angular upgrades.

## Nx and custom builders

`ng add` swaps only the standard `@angular/build` builders.
For other builders, it writes the manual setup below.
It keeps your builder and prints why.
That includes CLI 19 apps using `@angular-devkit/build-angular`.
For Nx with no `angular.json`, use manual setup.

## Manual setup

Keep this path if you need your own tool map.
`ng add` writes the hook, map, guard, and prod swaps.
Old `provideBuoy({ modules, ... })` calls still work.

Put the light hook first in `src/main.ts`:

```ts
import '@buoy-gg/angular/register';
```

Use a dev guard around your manual provider:

```ts
(typeof ngDevMode === 'undefined' || ngDevMode)
  ? provideBuoy({ modules: () => import('./buoy').then(m => m.modules) })
  : [],
```

Your map uses each tool's `/web` entry.
Manual setup needs its own Router, Query, and state links.
The guard alone can leave tool chunks in prod.
On CLI 20+, add `buoy-disabled` to prod `conditions`.
Swap the tool-list file for an empty file too.
CLI 19 and webpack need entry file swaps instead.
See the [full manual setup](https://www.npmjs.com/package/@buoy-gg/angular#manual-setup).

For phone apps, see [Capacitor and Ionic](../capacitor).
Web calls still follow the browser's CORS rules.
