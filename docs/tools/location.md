---
title: Location
seoTitle: "React Native Location Simulator: fake GPS, routes and geofences"
id: tools-location
description: "Pin your React Native app to any place, move it along a route with real heading and speed, trigger geofences, and test no GPS signal or location services off. Works with expo-location and the community geolocation libraries."
---

<!-- ::platform-badge platform="both" -->

Anything in your app that depends on where the phone is gets hard to test from a desk. Location tells your app it is somewhere else. Pin it to a store, drive it across town, walk it past a geofence, or turn the GPS off, and watch what your screens do. The phone's real location doesn't change.

Location is part of Buoy Pro.

## Install

<!-- ::PM npm="npm install @buoy-gg/location" yarn="yarn add @buoy-gg/location" pnpm="pnpm add @buoy-gg/location" bun="bun add @buoy-gg/location" -->

Location shows up in your `FloatingDevTools` menu on its own. If your app asks for the location or starts a geofence while it starts up, import it first in your entry file:

```js
// index.js
import "@buoy-gg/location";
import "expo-router/entry";
```

It works with the location library your app already uses:

- `expo-location`, including `watchPositionAsync`, heading, background location updates and geofencing (through `expo-task-manager`)
- `@react-native-community/geolocation`
- `react-native-geolocation-service`

You don't change your app's code.

## How to use it

The top card always says where your app is: the place or route, whether it's moving, and the app's location permission. The small map under it shows your places, your geofences, the route and the simulated position.

- Location. Tap one of your places to pin the app there. Other Location has a field for coordinates like `37.3349, -122.0090` or a Google or Apple Maps link, and a list of places around the world.
- Movement. Pick a route: your own, or Walk, Run, City Drive, Highway, Tunnel or Block Loop, which start where the app is now. Each update carries the heading and speed of the road. Change the speed while it plays, pause it, or choose Stop Here to stay where it got to.
- Conditions. Good gives precise fixes. Weak gives fixes that are about 65 m off and wander, like indoors. No Signal stops updates and makes requests for the position fail. Off tells your app location services are off for the whole phone. Accuracy & Drift sets exact numbers.
- Tap Use Real Location to stop.

A small strip stays on screen while the location is changed, so you don't forget. The changed location stays after a reload. Activity lists what your app asked for and what its tasks did.

## Your own places and routes

Add the places your app cares about, like your stores, and routes that pass by them:

```js
import { registerLocationPlaces, registerLocationRoutes } from "@buoy-gg/location";

registerLocationPlaces([
  { label: "Downtown store", latitude: 40.7608, longitude: -111.891 },
]);

registerLocationRoutes([
  {
    id: "walk-past-downtown",
    name: "Walk past Downtown",
    points: [
      { latitude: 40.7608, longitude: -111.8960 },
      { latitude: 40.7608, longitude: -111.8860 },
    ],
    speed: 1.4, // meters per second
  },
]);
```

## Geofences

When your app starts geofencing with `Location.startGeofencingAsync`, the tool lists every region with how far away it is, or Inside. A region at one of your places takes that place's name. Tap a region to cross its edge, or tap its circle on the map. Your task from `TaskManager.defineTask` runs with the same data it gets from the phone, and the row shows when it ran. A route that passes through a region enters it and then leaves it.

While Location is on, your geofences and background updates run in the tool, not on the phone. When you go back to the real location, they start on the phone again with the options your app gave them.

## Permission

Location doesn't change the location permission. If your app hasn't been allowed to use the location, its requests still fail, the same as on a real phone. To test "never asked", "denied", "blocked" or "approximate location only", use the [Permissions tool](./permissions). With approximate location, Location reports positions the way the phone does: rounded to a few kilometers.

## What stays real

Location changes what your app's JavaScript gets from its location library. Some things read the location in native code and keep the real one:

- The blue dot on a native map (`react-native-maps`, `expo-maps`, Mapbox). Draw your own marker from the location your app gets, or move the simulator itself: in the MCP server, `sim_location` sets the iOS Simulator's location.
- Other native SDKs, like analytics or ads.
- Geofence events the phone would deliver while your app is closed.

## FAQ

### Does this change the location on my phone?

No. Only your app sees the new location.

### Does it work on a real phone?

Yes. It runs inside your app, so it works on a real phone, a simulator and an emulator.

### Can AI agents use it?

Yes. The [MCP server](../mcp) has `get_location`, `location_action` and `sim_location`, and [Ask Buoy](./ask-buoy) can do it when you ask, like "walk me past the downtown store and tell me if the check-in shows up". Buoy Desktop has a Location panel too.
