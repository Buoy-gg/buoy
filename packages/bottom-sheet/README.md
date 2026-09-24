# @buoy-gg/bottom-sheet

A React Native component with bottom-sheet and floating-window modes, configurable headers and footers, and drag-to-resize gestures.

## Features

- 📱 **Dual Modes** - Bottom sheet and floating window modes
- 🎨 **Fully Customizable** - Custom headers, themes, and styles
- ✋ **Touch Gestures** - Drag to resize, double-tap to toggle mode, triple-tap to close
- 🔄 **Smooth Transitions** - Spring-based animations for natural feel
- 📐 **Smart Boundaries** - Respects safe areas and screen boundaries
- **Dependencies** - Requires React and React Native and uses Buoy’s shared UI package
- 🎯 **TypeScript** - Full type safety and IntelliSense support

## Installation

```bash
npm install @buoy-gg/bottom-sheet
# or
yarn add @buoy-gg/bottom-sheet
# or
pnpm add @buoy-gg/bottom-sheet
```

## Basic Usage

```tsx
import React, { useState } from 'react';
import { View, Text, Button } from 'react-native';
import { BottomSheet } from '@buoy-gg/bottom-sheet';

function MyComponent() {
  const [visible, setVisible] = useState(false);

  return (
    <View>
      <Button title="Open Bottom Sheet" onPress={() => setVisible(true)} />

      <BottomSheet
        visible={visible}
        onClose={() => setVisible(false)}
        header={{
          title: "My Bottom Sheet",
          subtitle: "Drag to resize"
        }}
      >
        <View style={{ padding: 20 }}>
          <Text>Your content here!</Text>
        </View>
      </BottomSheet>
    </View>
  );
}
```

Open the sheet, drag its header, switch modes, and close it to check the basic integration.

## Advanced Usage

These snippets reuse `visible`, its setter, and your own content or save handler from the surrounding component.

### Custom Theme

```tsx
<BottomSheet
  visible={visible}
  onClose={() => setVisible(false)}
  theme={{
    primary: '#FF6B6B',
    secondary: '#4ECDC4',
    background: '#1A1A2E',
    panel: '#16213E',
    success: '#06FFA5',
  }}
>
  <YourContent />
</BottomSheet>
```

### With Footer

```tsx
<BottomSheet
  visible={visible}
  onClose={() => setVisible(false)}
  footer={
    <View style={{ padding: 16, borderTopWidth: 1, borderTopColor: '#ccc' }}>
      <Button title="Save" onPress={handleSave} />
    </View>
  }
  footerHeight={60}
>
  <YourContent />
</BottomSheet>
```

### Custom Header

```tsx
<BottomSheet
  visible={visible}
  onClose={() => setVisible(false)}
  header={{
    customContent: (
      <View style={{ padding: 16 }}>
        <Text style={{ fontSize: 18, fontWeight: 'bold' }}>
          Custom Header
        </Text>
      </View>
    )
  }}
>
  <YourContent />
</BottomSheet>
```

### Floating Window Mode

```tsx
<BottomSheet
  visible={visible}
  onClose={() => setVisible(false)}
  initialMode="floating"
  onModeChange={(mode) => console.log('Mode changed to:', mode)}
>
  <YourContent />
</BottomSheet>
```

### Height Control

```tsx
<BottomSheet
  visible={visible}
  onClose={() => setVisible(false)}
  initialHeight={500}
  minHeight={200}
  maxHeight={800}
>
  <YourContent />
</BottomSheet>
```

## Props

### BottomSheetProps

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `visible` | `boolean` | **required** | Controls visibility of the bottom sheet |
| `onClose` | `() => void` | **required** | Callback when sheet should close |
| `children` | `ReactNode` | **required** | Content to display in the sheet |
| `header` | `BottomSheetHeaderConfig` | `undefined` | Header configuration |
| `styles` | `CustomStyles` | `{}` | Custom styles for container and content |
| `minHeight` | `number` | `100` | Minimum height in bottom sheet mode |
| `maxHeight` | `number` | `screen height - safe area` | Maximum height in bottom sheet mode |
| `initialHeight` | `number` | `400` | Initial height in bottom sheet mode |
| `initialMode` | `'bottomSheet' \| 'floating'` | `'bottomSheet'` | Initial display mode |
| `onModeChange` | `(mode) => void` | `undefined` | Callback when mode changes |
| `footer` | `ReactNode` | `undefined` | Footer content (sticky at bottom) |
| `footerHeight` | `number` | `0` | Height of footer for content padding |
| `onBack` | `() => void` | `undefined` | Callback for back action (enables top-left corner tap) |
| `theme` | `Partial<Theme>` | `defaultTheme` | Custom color theme |

### BottomSheetHeaderConfig

| Prop | Type | Description |
|------|------|-------------|
| `title` | `string` | Header title text |
| `subtitle` | `string` | Header subtitle text |
| `customContent` | `ReactNode` | Custom header content (replaces title/subtitle) |
| `hideCloseButton` | `boolean` | Hide the close button |

## Gestures

- **Drag Header** - Resize bottom sheet or move floating window
- **Double Tap Header** - Toggle between bottom sheet and floating modes
- **Triple Tap Header** - Close the sheet
- **Drag Corner (Floating)** - Resize floating window
- **Tap Top-Right Corner (Floating)** - Close the sheet
- **Tap Top-Left Corner (Floating)** - Trigger back action (if `onBack` is provided)
- **Fast Swipe Down** - Close bottom sheet

## Performance

The component uses React Native animations and gestures. Actual frame rate depends on device load, sheet contents, and your app. Profile dragging and resizing with representative content.

## TypeScript

Full TypeScript support with exported types:

```tsx
import {
  BottomSheet,
  BottomSheetProps,
  BottomSheetMode,
  BottomSheetHeaderConfig
} from '@buoy-gg/bottom-sheet';
```

## License

MIT

## Credits

Based on the proven JsModal component from the React Native Buoy toolkit.
