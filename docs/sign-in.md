# Sign in with Buoy

Your Buoy account turns on Buoy, the same way a key does. You sign in once on each computer, browser or site.

## Your dev builds

Run this in your app's folder:

```bash
npx --package=@buoy-gg/core buoy login
```

Your browser opens buoy.gg. Click Allow. The CLI then writes a dev token to your env file, under the same name a key used to have, such as `EXPO_PUBLIC_BUOY_KEY` or `VITE_BUOY_KEY`. Pass it to Buoy as you would a key.

A dev token only works in dev builds and sims. If one ends up in a shipped app, it opens nothing there. It lasts 30 days. After that, Buoy asks you to run `buoy login` again.

On Expo, Vite and Next.js, the token goes in `.env.development.local`. Other projects get `.env.local`.

Two more commands:

```bash
npx --package=@buoy-gg/core buoy whoami   # are you signed in?
npx --package=@buoy-gg/core buoy logout   # sign this computer out
```

## Desktop and the MCP

In Buoy Desktop, click Sign in with buoy.gg. Desktop keeps your sign-in in your computer's keychain, and you stay in after restarts. Sign out ends it on buoy.gg too. Your settings follow you to Desktop as well.

The MCP uses the sign-in from `buoy login`. You don't need a key in your project for the MCP.

## Your live site

A live site can turn on Buoy for your own admins and testers, with no key in the build.

1. Add your site at [buoy.gg/dashboard/sites](https://buoy.gg/dashboard/sites). Use its address, like `https://app.example.com`.
2. Add `signIn` to `FloatingDevTools`:

```tsx
<FloatingDevTools modules={modules} signIn />
```

A "Sign in to Buoy" button shows in the corner. It opens a buoy.gg window that asks to let your site use Buoy. After that, Buoy opens, and it stays open after reloads.

Only show `FloatingDevTools` to your own people. Your app decides who sees it, the same as before.

Buoy on a live site needs a Pro or Business plan. A free account sees a note that asks for a plan.

## Phones and test builds

A phone app can't open a sign-in window. So it shows a code instead, as a QR code and as text. Use it for TestFlight, QA and store builds, where no key is in the app.

1. Add your app at [buoy.gg/dashboard/sites](https://buoy.gg/dashboard/sites). Type `app:` and then its id, like `app:com.acme.shop`. That is the iOS bundle id or the Android package.
2. Add `signIn` to `FloatingDevTools`:

```tsx
<FloatingDevTools signIn />
```

Tap "Sign in" in the corner. Scan the QR code with any phone that is signed in to buoy.gg. Or go to buoy.gg/activate and type the code. Check the code, then tap Allow. Buoy opens in the app a few seconds later.

Buoy reads the app id from your Expo config. If it can't, pass it in: `signIn={{ appId: "com.acme.shop" }}`.

A code lasts 10 minutes and works once. Only allow a code shown on a device in your hand. If someone sends you a code, don't type it in.

The same plan rule applies: Buoy in a test or store build needs Pro or Business.

## How long you stay in

You stay signed in as long as you use Buoy at least once every 60 days. Buoy renews your sign-in in the background.

With no internet, Buoy keeps working for 7 days. Then the tools lock until it can check in.

If someone leaves your team, or you remove a site, Buoy stops for them within 12 hours.

## Your settings follow you

Sign in on two devices. Change a setting on one, and it shows up on the other. This works with a sign-in or a dev token. Plain keys and bot keys don't sync.

Only your settings move. Web calls you saved and other data Buoy caught stay on the device.

A new setting goes out a few seconds after you change it. Other devices get it when they start, or within 15 minutes.

## Teams

Business teams are run from the [Team page](https://buoy.gg/dashboard/team). A team admin can:

- invite people by email, up to the seats you bought;
- give each person a role, like dev or qa;
- remove people, which frees their seat;
- list the team's live sites, which every member then uses;
- list web calls to hide for the whole team;
- list React Query queries to hide, or the only ones to show.

Team lists add to each person's own lists. In the app, team items show a TEAM badge.

## Keys still work

You don't have to switch. A key you already use keeps working everywhere, and keys are staying. Use one if you'd rather not sign in, or for a build where no one can sign in.

## Keys for bots and CI

Bots and CI can't click a sign-in button. On the Team page, a team admin can make a bot key. It starts with `bsk1_`. You see it once, so copy it right away.

Put the bot key where a license key goes, such as a CI secret that sets `EXPO_PUBLIC_BUOY_KEY`. Buoy trades it for a pass that lasts a day. Delete the key on the Team page, and the bot stops within a day.
