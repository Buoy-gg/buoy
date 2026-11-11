---
title: Security
seoTitle: "Buoy Security — where your app data goes, what is hidden, and who can connect"
id: security
description: "How Buoy keeps your app data safe. Where it goes, what is hidden, who can connect, what a company can lock, and what we have not done yet."
---

This page is for the one who says yes before a team can use Buoy. It tells you where your app's data goes. It tells you who can see it. It also lists what we have not done yet.

## The short answer

- Your app's data goes to your own Mac or PC. It does not go to Buoy.
- Tokens, cookies and keys are hidden before they leave the app.
- Only Buoy Desktop and the Buoy MCP on your Mac or PC can read your app.
- Buoy is off in a shipped app unless you turn it on.
- A company can lock Buoy to one Mac or PC and keep secrets hidden for good.

## Where your data goes

Buoy runs a small server on your Mac or PC, on port 42831. We call it the broker. Your app sends its tool data there. Buoy Desktop and the MCP read it from there.

That is the whole trip. Your web calls, saved values, logs and app state stay there.

Four things do talk to `buoy.gg`. Each one is small. Each has its own page.

| What | What it sends | Read more |
|---|---|---|
| Account check | What we need to check your plan | [Sign in with Buoy](./sign-in) |
| Install ping | Five plain facts, once a day, in dev builds only | [Telemetry](./telemetry) |
| A bug you share | The report, after private data is taken out on the phone | [Bug Links](./tools/bug-links) |
| Ask Buoy | Your chat and tool results, sent to the AI model you pick | [Ask Buoy](./tools/ask-buoy) |

You can turn off the install ping. You never have to share a bug or use Ask Buoy.

## Secrets are hidden first

Your app covers login tokens, cookies and keys before it sends anything. Buoy Desktop and the MCP see `[hidden by Buoy]` where a secret was.

This works the same in React Native, web, Swift, Flutter, Android and MAUI.

A dev can click **Secrets hidden** in Buoy Desktop to see real values. That switch has rules:

- Only Buoy Desktop's own window can turn it on. A script or an AI agent can't.
- Only that window gets the real values. Other programs still get them hidden.
- It turns off by itself 10 minutes after Desktop is closed.
- A shipped app keeps secrets hidden unless you allow the switch by name.
- A company can turn the switch off for good.

Names, emails and other user data are not hidden. No filter is perfect. See the list at the end of this page.

[How secrets are hidden](./desktop#secrets-are-hidden-before-they-leave-the-app)

## Who can connect

The broker treats each thing that connects as an app. To be more, it has to prove it.

- **A dashboard** is Buoy Desktop or the MCP. It proves itself with a token. The token is in a file that only your user can read.
- **An app** can send its own data and get actions meant for it. It can't see other apps or the device list.
- **A website** from outside your network has to ask. You say yes or no in a pop-up.

Two apps on two machines can't share one device id. The second one is turned away. The Desktop log says where it came from.

[Who can connect](./desktop#who-can-connect)

## Phones on Wi-Fi

Buoy listens on your Wi-Fi so a real phone can connect with no setup. That data is not encrypted. Use a network you trust.

Don't want that? Turn off **Phones on Wi-Fi** in Buoy Desktop. Then only your own Mac or PC can connect. Sims and USB phones still work.

## Shipped apps

Buoy does not connect from a shipped app. You have to turn that on, and it needs a paid plan.

When you do turn it on:

- Desktop and the MCP can read but not change things. You can allow a tool by name.
- The app only connects to `localhost` or over `https://`, unless you allow plain `http://` by name.
- Secrets stay hidden.
- The install ping is never sent.

[Release builds](./desktop#release-builds)

## AI agents

An AI agent uses Buoy through the MCP. The MCP adds its own guard.

- It hides secrets again, even when Desktop shows them.
- It can't turn on Show secrets. The broker refuses it.
- It will not write a hidden value back into your app.
- It marks text from your app as data. The agent should not take it as an order.

## What a company can lock

An admin can put one small file on each Mac or PC. A person who is not an admin can't undo it.

```json
{ "allowNetworkDevices": false, "allowShowSecrets": false }
```

- The first rule keeps Buoy on one Mac or PC. No phones on Wi-Fi.
- The second rule keeps secrets hidden. No one can show them.

Buoy Desktop and the MCP both follow this file.

[Lock it for a whole company](./desktop#lock-it-for-a-whole-company)

## How we ship Buoy

- Our npm packages do not run code when you install them.
- A check runs before each release. It stops any package that would run code at install.
- We wait 3 days before we take a new version of code we did not write. Most bad releases are caught in that time.
- Our build steps on GitHub are pinned to exact versions, so they can't change under us.
- Buoy Desktop for Mac is signed, and Apple checks it before you get it.

## What we have not done yet

We would rather tell you than have you find out.

- **Wi-Fi data is not encrypted.** A phone on Wi-Fi sends its data as plain text on your network. Secrets are hidden first. You can turn Wi-Fi phones off.
- **Programs you run are trusted.** Any program that runs as you can read the token file. It can then read your app's data, with secrets hidden. It can't show the secrets while Buoy Desktop is open. If Desktop is closed, such a program could take its place and ask your app for them.
- **The secret filter can miss.** Think of a secret with no digit, under a plain name like `v`. It can get through until Buoy has seen it in a header.
- **Buoy Desktop for Windows is not signed yet.**
- **npm can't yet prove a package came from our build.** That proof needs a public build. Our code is private.

## Tell us about a problem

Found something that looks unsafe? Please tell us before you post it. Write to [support@buoy.gg](mailto:support@buoy.gg).
