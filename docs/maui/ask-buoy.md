---
title: Ask Buoy
seoTitle: "Ask Buoy in .NET MAUI"
id: maui-ask-buoy
description: "Chat with Buoy inside your MAUI app. Read live state and review changes before they run."
---

Ask Buoy is a chat inside your MAUI app.
It reads the live tools you have set up.
It can help find faults and change test state.
The chat needs Pro.
The hosted chat also needs you to sign in.

## Set up chat

Add the tools your app needs before `Build`.
Use this with your one `UseBuoy` call.

```text
#if DEBUG
using Buoy.Core.Agent;
using Buoy.Maui;
#endif

#if DEBUG
builder.UseBuoyNetwork();
builder.UseBuoyStorage();
builder.UseBuoyAskBuoy(options =>
{
    options.Context = new ContextPack(
        Notes: ["The home page lists saved items."]);
});
builder.UseBuoy(options =>
{
    options.SignIn = new BuoySignInOptions();
});
#endif
```

Set up the app ID and sign-in with [Install](./installation).
A Pro key alone does not create a hosted session.
Open Buoy, sign in, then open Ask Buoy.

## What it can do

Chat can read logs, web calls, and app state.
It can edit known store keys or add mock replies.
It can change test time, places, and grant states.
It can open paths that your Routes tool knows.
In Debug builds, native screen tools can read and tap views.
Each task needs the right [tool setup](./tools).

Try a task with a clear target:

- "Why did the last web call fail?"
- "Set the saved theme key to dark."
- "Show the last five error logs."

MAUI has no React tree or React store tools.
Chat cannot add a missing tool at run time.
The app reload and relaunch actions have no hook here.
Not all changes can be undone.

## Write approval

By default, a destructive step needs your OK.
A write in a question turn needs your OK too.
Some steps that only look up data can still run.
A task that asks for a change may run it directly.
Read only mode blocks writes.

Use this rule to ask before each write:

```text
builder.UseBuoyAskBuoy(options =>
{
    options.Policy.RequireApproval = ["write", "destructive"];
});
```

Keep Bypass approvals off in the chat's settings.
That switch can skip the app's approval list.
It does not bypass the app's Read only rule.
Writes in question turns still need a tap.
Use `options.Policy.ReadOnly = true` to block writes in code.

The Changes bar lists what chat has changed.
Use Undo for changes that have an undo path.
A destructive action may have no way back.
Read the approval card before you let it run.

## App notes and your own host

`Context` holds notes about your app.
`GetContext` can return fresh notes on each turn.
Keep model keys on your own server.

The default host is `https://ai.buoy.gg/v1/chat/completions`.
For your own gateway, set these fields:

```text
builder.UseBuoyAskBuoy(options =>
{
    options.Hosted = false;
    options.Endpoint = "https://your-host/v1/messages";
    options.Protocol = "anthropic";
    options.Model = "your-model";
    options.Headers = async (meta, token) =>
        await GetHeadersAsync(token);
});
```

Supply `GetHeadersAsync` from your app's own auth flow.
It runs for each web call.
Use `openai` for an OpenAI-style gateway instead.
Tool data and chat text go to the chosen host.
Secure store value reads are off by default.
Setting `options.Policy.SecureReads = true` allows those reads.
Those values can then reach the model host too.

## Saved chat and Desktop

Chat saves turns and the undo list by default.
Saved model history leaves out raw tool results.
Set `PersistTranscript = false` to turn chat saves off.

Desktop can read the chat, undo, or clear it.
It cannot send chat text or answer an approval.
Approve those steps in the app on your phone.
