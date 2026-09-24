---
title: Floating Tools
seoTitle: "React Native Floating Tools — custom controls on top of your app"
id: custom-tools-floating-tools
description: "Put a few app-specific controls over your React Native or web app without opening a modal. Drag the grip to move it, tap the grip to hide it."
---

A floating tool puts a few controls over your app without opening a modal. Drag its grip to move it. Tap the grip to hide it at the right edge, then tap again to restore it.

Two built-in tools ship a strip of their own: Network's throttling controls and Time Machine's restore bar, both opened from the tool's header. The DevTools bubble and custom strips render through the same `FloatingTool` implementation on each platform. Native consumers share `DraggableHeader`, position storage, hide/restore behavior and animations. Browser consumers share the existing pointer-event renderer and floating-tools state store. Tool content does not implement gestures.

## Install

```bash
pnpm add @buoy-gg/shared-ui
```

React Native apps can import these APIs from `@buoy-gg/shared-ui`. React DOM apps should use `@buoy-gg/shared-ui/floating`, which selects the browser renderer. React is a peer dependency; the native renderer also needs React Native. No Buoy account, Network package, or DevTools provider is required for a custom floating tool.

## Mount the host once

Place the host alongside your app's navigation, inside a full-screen root. It passes touches through the empty space around tools. It must stay mounted while users navigate.

```tsx
import { View } from 'react-native';
import { FloatingToolHost } from '@buoy-gg/shared-ui';

export function App() {
  return (
    <View style={{ flex: 1 }}>
      <YourNavigation />
      <FloatingToolHost />
    </View>
  );
}
```

`FloatingDevTools` already mounts this host. Do not mount a second one in an app using DevTools. Native system modals can appear above an app-level overlay; the host does not bypass platform modal layering.

## Build a custom controller

```tsx
import { useState } from 'react';
import { Text, TouchableOpacity } from 'react-native';
import {
  FloatingTool,
  openFloatingTool,
  type FloatingToolRenderContext,
} from '@buoy-gg/shared-ui';

function RecordingTool({ id, revealToken, dismiss }: FloatingToolRenderContext) {
  const [recording, setRecording] = useState(false);

  return (
    <FloatingTool
      id={id}
      revealToken={revealToken}
      variant="strip"
      accessibilityLabel="Move or hide recording controls"
    >
      <Text style={{ color: 'white' }}>
        Recorder: {recording ? 'On' : 'Off'}
      </Text>
      <TouchableOpacity
        accessibilityRole="button"
        onPress={() => setRecording(value => !value)}
        style={{ padding: 12 }}
      >
        <Text style={{ color: 'white' }}>{recording ? 'Stop' : 'Start'}</Text>
      </TouchableOpacity>
      <TouchableOpacity accessibilityRole="button" onPress={dismiss} style={{ padding: 12 }}>
        <Text style={{ color: 'white' }}>Close</Text>
      </TouchableOpacity>
    </FloatingTool>
  );
}

export function openRecorder() {
  openFloatingTool({
    id: 'com.example.recorder',
    render: context => <RecordingTool {...context} />,
  });
}
```

The sample only changes a label. Connect Start and Stop to your recorder or feature-flag API. For ongoing operations, complete your Stop action before calling `dismiss()`.

For React DOM, use the `/floating` import and ordinary `button` and `span` elements inside the same component. Mount `<FloatingToolHost />` alongside your app. The browser grip also supports Enter and Space to hide or restore.

## Lifecycle and state

- Give each tool a stable, app-wide unique `id`. Use the same id in its registration and its `FloatingTool` props.
- Opening an existing id restores that instance and preserves its component state. `revealToken` lets the shared shell bring a hidden instance back.
- Hiding moves the tool; it does not stop an operation or unmount its content.
- `dismiss()` removes that tool's UI. It does not stop external work automatically. The tool controller owns that decision.
- Position persistence defaults to enabled, with separate keys for each id. Pass `enablePositionPersistence={false}` for a temporary tool.
- Reload restores positions when tools are opened again. It does not automatically open tools or restart their actions.
- Render a `FloatingTool` directly for a permanently mounted controller if you do not need the host registry.

## Layout

`variant="strip"` gives the larger control strip. The default `bubble` variant preserves the DevTools bubble layout. The shell supplies the grip and boundary handling; keep the content compact enough to fit the screen and give actions clear labels.

Set `dragFromSurface` to let users drag the background and non-interactive content with the same shared movement logic. Buttons keep their own gestures. Background taps do nothing; the grip still toggles hide and restore. The default is grip-only dragging.

Use `accessory` for a small profile picker or options panel attached to the strip. Use `handleAdornment` for a status dot that remains visible when the strip is hidden. Place clickable controls in the content area, not inside the drag grip.

Custom tools use independent positions. Move them apart when opening several at once. The host does not automatically rearrange existing tools.

## Verify your integration

The example app's **Network playground → Try custom floating tools** page runs two independent custom tools over an ordinary app button. It is the quickest way to see how the host, the saved positions and hide/restore behave together before you wire your own.

In your own app, check the strip near the screen edges, on a narrow screen and after rotation, and confirm your content's buttons still respond when `dragFromSurface` is on. Open two tools at once to see that their positions are independent.

Floating tools are new. The shared shell is covered by unit and browser tests, but native gesture and layout behaviour has not been validated across the full range of devices yet, so confirm the interactions you depend on in your own build.

### Restore an open tool after reload

Call `registerPersistentFloatingTool(registration, storage)` once at app startup. Pass the same stable `id` and `render` used with `openFloatingTool`, plus storage with async `getItem` and `setItem` methods. React Native consumers can pass `persistentStorage` from `@buoy-gg/shared-ui`. The registration remembers opening and dismissal; `FloatingTool` continues to own position storage. Restoring preserves the saved hidden state. Opening the tool explicitly reveals it.

The registration returns an unsubscribe function. Register only tools whose render context is available at startup; a remote device connection must be re-established before restoring controls for it. This API persists visibility, not the tool's action state.
