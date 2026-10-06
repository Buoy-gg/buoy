---
title: Ask Buoy
seoTitle: "React Native AI Devtool — let QA and support drive your app in plain English"
id: tools-ask-buoy
description: "An in-app AI chat that uses your installed Buoy tools. A tester types \"make the double burger out of stock and put it in my cart\" and it happens, with each step shown and undo for most changes."
---

<!-- ::platform-badge platform="both" -->

<!-- ::tool-film id="ask-buoy" -->

Ask Buoy is an AI chat inside your app. It uses the Buoy tools you installed. It can read app data, like web calls and storage. It can also make changes, like editing storage or faking a web call. It needs Pro, Business or a trial. It is in beta.

Use Buoy's hosted AI, or point it at your own model.

<!-- ::ask-buoy-live-demo -->

A few things people ask:

- *"Make the next checkout call fail with a 500"* (Network)
- *"Show me what user 8823 sees on the rewards screen"* (Impersonate)
- *"Give me 1500 points"* (Storage or a state tool)

---

## Before you start

- **Plan.** Pro, Business or a trial. See [pricing](https://buoy.gg/pricing).
- **Tools.** Ask Buoy can only use tools you installed. Set those up first.
- **Versions.** Keep all Buoy packages on the same version. Hosted AI needs 7.0.54 or later. Check with `npm ls @buoy-gg/license`.
- **Build.** Use a dev build for fake web calls and for tapping through screens. Some of those actions only work when `__DEV__` is true. A release build can still read data, sign in as a test user and change screens. Ask Buoy refuses what can't work in your build, and says why.
- **Web.** Import from the package's `/web` entry (7.0.41 or later). See [Web installation](../web/installation#tool-setup).

## Installation

<!-- ::PM npm="npm install @buoy-gg/ask-buoy" yarn="yarn add @buoy-gg/ask-buoy" pnpm="pnpm add @buoy-gg/ask-buoy" bun="bun add @buoy-gg/ask-buoy" -->

It shows up in the dial as **ASK BUOY**.

---

## Hosted Ask Buoy (beta)

This is the easy way. You need no AI key and no server. Buoy runs the AI, and your plan comes with credits each week.

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { hostedAskBuoy } from "@buoy-gg/ask-buoy";

<FloatingDevTools signIn askBuoy={hostedAskBuoy({ policy: { readOnly: true } })} />
```

`readOnly` lets it look but not change things. Remove it once you trust what it does.

Each person signs in with Buoy in your app. They scan the QR code or type the code at buoy.gg/activate. That adds your app to your account's Sites list. On a team, only an admin can add it. See [Sign in with Buoy](../sign-in).

### Credits

| Plan | Credits each week | About how many asks |
| --- | ---: | ---: |
| Pro | 480 | 140 |
| Business | 1,200 per seat, shared by the team | 350 per seat |
| Trial | 1,000, once | 300 |

One credit is $0.001 of AI use. Most asks cost 2 to 4 credits. One ask never costs more than 100. Credits reset each Monday at 00:00 UTC. Unused credits don't carry over.

See what's left in Ask Buoy's settings. You can also see it on buoy.gg under Dashboard, then Billing.

### When something goes wrong

Each error shows a short code and a request id. Tap **Copy details** to copy them. Tap **Report problem** to send them to us. A report never has your chat in it.

| What you see | What it means |
| --- | --- |
| You used this week's credits | They come back on the date shown. |
| Sign in to use hosted Ask Buoy | Sign in with Buoy in the app first. |
| Hosted AI is paused right now | We paused it for a short time. Try again later, or use your own model. |

---

## Use your own model

Want your data to stay with your own AI company? Point Ask Buoy at your own server.

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools
  askBuoy={{
    endpoint: "https://ai.acme.com/v1/messages",
    protocol: "anthropic",   // or "openai" — covers Azure, Gemini-compat, most gateways
    model: "YOUR_MODEL_ID",
    policy: { readOnly: true },

    // Called before every request, so short-lived tokens work.
    // Your gateway must validate the token and authorize the request.
    headers: async () => ({ Authorization: `Bearer ${await auth.getToken()}` }),
  }}
/>
```

Your server must check the token and the user. It should limit which models and how big a request can be. It must stream answers back. Keep your AI key on the server.

`apiKey` can call an AI company straight from the app. But the key ends up inside the app. Use it only on your own machine, and never give that build to testers.

---

## Try one safe ask

Send *"List the installed tools."* You should see a short list of your tools. Nothing in the app should change. Then ask it to read one web call. Check that the answer matches the Network tool.

## Changes and Undo

By default it reads and makes normal changes right away. Big changes, like wiping storage, wait for your tap. The changes bar at the top counts what it changed. Tap **Undo** to put most of it back. A change it can't undo is marked **permanent**. You can also type *"undo that"*.

You can make it ask before every change, or allow only some tools. See [In the chat](./ask-buoy/in-the-chat) and the `policy` option in [Teach it your app](./ask-buoy/teach-it-your-app#configuration-reference).

## What it sends, and what it saves

**Where the chat goes.** With hosted AI, it goes through Buoy's server to OpenAI's GPT-6 Luna model, by way of OpenRouter. Buoy keeps the cost of each ask, but not the chat. The first time someone uses it, Ask Buoy shows a note about this before it sends anything. With your own model, the chat goes only to your server.

**What each ask sends.** Your message goes first. Then your `context` notes and types. Then a sketch of the app: route paths, store names, storage key names and field names. It also sends the screen you're on with its route params, which query keys are on screen and your last few web call URLs. Params, keys and URLs can hold ids. Tool results go too, like storage values and web call bodies. It strips anything that looks like a secret first. SecureStore values stay off unless you set `policy.secureReads: true`.

**What stays on the phone.** The chat is saved so it comes back after a reload. Tool results are not saved. The undo list is saved, and it holds the old and new value of each change so Undo can work. A change that holds a secret is dropped from it. Turn saving off with `persistTranscript: false`.

**Be careful with real user data.** App data can come from places an attacker controls. Text in that data could trick the AI. Use `readOnly` when you point it at real user data.

See [Telemetry](../telemetry) for what Buoy sends about your account.

## If it doesn't work

- **A strange error on the first message.** Your `endpoint` and `protocol` don't match. Ask Buoy warns you when it can tell.
- **The answer shows up all at once.** React Native's own `fetch` can't stream. Buoy switches to a different way after the first answer. On Expo, you can also use `expo/fetch` as the global `fetch`.
- **"does not work in this build".** That action needs a dev build. It's not a bug.

More fixes are in [In the chat](./ask-buoy/in-the-chat#troubleshooting).

## Go further

- [Teach it your app](./ask-buoy/teach-it-your-app): notes, types, team steps, your own tools and every option.
- [In the chat](./ask-buoy/in-the-chat): approvals, Stop, thinking, saved chats and Buoy Desktop.
- [Scenarios](./scenarios): save a setup that worked as a one-tap button.
- [Network overrides](./network): the fake web calls Ask Buoy makes.
- [Impersonate](./impersonate): see the app as one user.
