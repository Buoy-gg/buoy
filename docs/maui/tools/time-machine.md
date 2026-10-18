---
title: Time Machine
seoTitle: "Time Machine for .NET MAUI"
id: maui-tools-time-machine
description: "Save your MAUI app's state as a restore point. Put it back with one tap."
---

Save how your app is right now.
Later, put it back with one tap.
So you can test the same spot again and again.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyTimeMachine();
```

## What it saves

Each part of your app it can save is a source.

- **Storage** saves your Preferences keys.
  It also saves the SecureStorage keys you added to Buoy.
  This needs [Storage](./storage) set up.
- **Route** saves the screen you were on.
  This needs [Routes](./routes) set up with a Shell.
- **Your own data** can be a source too.
  See the last part of this page.

Buoy never saves its own keys.
So a restore point never holds another one.

Each source has a switch in the list.
Turn one off, and Time Machine leaves it alone.
It is not saved, and it is not put back.

## Save a restore point

Type a name and tap **Capture**.
With no name, Buoy names it for you.
It uses the screen and the time.

Restore points stay saved after the app closes.
The free plan keeps 1 restore point.
Pro keeps as many as you want.

## Put it back

Tap **Restore** on a row.
Your data comes back.
The app also goes back to the saved screen.
Then the tool gets out of the way.

If a source could not come back, a card tells you why.

Swipe a row to see more:

- **Bar** puts it on the action bar.
- **Dup** makes a copy.
- **Delete** removes it.
  You get 8 seconds to undo.

## Look before you restore

Tap a row to see what would change.
Each source shows how many items differ.
Open one to see each item, old and new.
Tap an item to see the full change.

Uncheck an item to leave it alone.
Buoy saves your choice on the restore point.
So every later restore skips it too.

You can also rename the point here.
And you can turn off the trip back to its screen.

## The action bar

Tap **Action bar** at the top.
A small bar floats over your app.
It holds one restore point.

- Tap **Restore** to put it back.
- **Undo** shows for 10 seconds after.
  It puts back what you had just before.
- Tap **+** to save a new point and use it.
- Tap the arrow to pick a different one.

The arrow also has a switch.
Turn it on to reload the app after each restore.
Use it for screens that read saved data only once.

## Start over

**Wipe All App State** deletes your app's saved keys.
Then the app reloads, like a fresh install.
Your restore points stay.

## Reload the app

**Reload app** builds your main page again.
Your saved data shows up fresh.
Data in your own objects does not reset.
To change what a reload does, set `Reload`.

```text
builder.UseBuoyTimeMachine(options =>
    options.Reload = () => RestartMyAppAsync());
```

## Save your own data

Data that only lives in memory needs its own source.
Make a class with `IStateSnapshotSource`.
It saves your data as JSON and puts it back.
Then add it once, when the app starts.

```text
using Buoy.Core.TimeMachine;

StateSnapshotRegistry.Register(new CartSource());
```

Your source shows up in the list like the others.

See [all MAUI tools](../tools).
