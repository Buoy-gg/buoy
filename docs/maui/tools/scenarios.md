---
title: Scenarios
seoTitle: "Scenarios for .NET MAUI"
id: maui-tools-scenarios
description: "Set up a test state in your MAUI app with one tap. Then undo it with one tap."
---

A scenario is a test setup you can run with one tap.
Its steps can fake a web call, write storage, act as a user or go to a page.
A **SIMULATED** banner stays on screen while it is on.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyScenarios(options =>
{
    options.CurrentBuild = "1.0.0";
    options.AddScenariosJson(() => FileSystem.OpenAppPackageFileAsync("scenarios.json"));
});
```

Each step uses a tool you already have.
Use Network to fake a web call and Storage to write keys.
Use Impersonate to act as a user and Routes to go to a page.
If a step needs a tool you did not add, Buoy will not run it.
The preview tells you which tool is missing.

## Write scenarios

Scenarios are data, written as JSON.
They use the same shape as `defineScenario()` in React Native.
So one file can serve both apps.

```json
{
  "id": "first-launch",
  "name": "First launch",
  "folder": "Onboarding",
  "steps": [
    { "tool": "storage", "action": "async.removeItem", "params": { "key": "@onboarding_done" } }
  ],
  "undoSteps": [
    { "tool": "storage", "action": "async.setItem", "params": { "key": "@onboarding_done", "value": "true" } }
  ]
}
```

`AddScenariosJson` takes one scenario, a list, or `{ "scenarios": [...] }`.
These show under **Shipped with this app**.
You can't change them on the phone.

`CurrentBuild` is your app's version.
A scenario made for a different version gets a badge, so you know to check it.

## Run one

1. Tap a row to see each step it will take.
2. Tap **Run**. Pick values for any choices it has.
3. The preview lists any problems before anything changes.
4. Tap the run button. The tool tucks away, and the banner shows.

Folders sort scenarios by flow, like Checkout or Login.
Tap a folder chip at the top to see only that flow.

## Turn it off

Tap **Deactivate** on the active card.
Or press and hold the stop button on the banner.

A report then says what was undone.
Fake web calls are removed.
Storage writes can't be undone on their own.
The report names each key that is still changed.
Add `undoSteps` to a scenario to put those keys back.

## What is not in MAUI yet

React Native can record what you do as a scenario.
MAUI can't record yet.
