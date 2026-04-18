---
title: Image Overlay
id: tools-image-overlay
---

<!-- ::platform-badge platform="both" -->

Overlay design mockups directly on your running app to compare against your implementation. Adjust opacity, scale, and position to get pixel-perfect results — no more switching between your design tool and the simulator.

<!-- ::image-overlay-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/image-overlay" yarn="yarn add @buoy-gg/image-overlay" pnpm="pnpm add @buoy-gg/image-overlay" bun="bun add @buoy-gg/image-overlay" -->

That's it. Image Overlay appears in your FloatingDevTools menu.

---

## Marking Components as Targets

To make a component discoverable in **Component Mode**, add a `testID` with the `image-target:` prefix:

```tsx
<View testID="image-target:LoginCard">
  <Text>Welcome back</Text>
  <TextInput placeholder="Email" />
  <Button title="Sign In" />
</View>
```

The text after `image-target:` becomes the label shown in the target list. Add as many targets as you like throughout your app:

```tsx
// Header section
<View testID="image-target:Header">
  <Logo />
  <NavBar />
</View>

// Product card
<View testID="image-target:ProductCard">
  <Image source={product.image} />
  <Text>{product.name}</Text>
  <Text>{product.price}</Text>
</View>

// Bottom tab bar
<View testID="image-target:TabBar">
  <TabButton icon="home" />
  <TabButton icon="search" />
  <TabButton icon="profile" />
</View>
```

> **Only `image-target:` testIDs are scanned** — your existing `testID` props for testing (e.g., `testID="login-button"`) are not affected and won't appear in the target list.

---

## Loading Images

You can load a design mockup in two ways:

- **Paste from Clipboard** — Copy an image in Figma (or anywhere) and tap "Paste from Clipboard". Requires `expo-clipboard`.
- **Enter a URL** — Paste a direct image URL and tap "Load". Works with any React Native setup.

> **Clipboard paste requires Expo.** The "Paste from Clipboard" feature uses `expo-clipboard` to read image data. If you're using bare React Native without Expo, use the URL method instead.

---

## Two Modes

### Component Mode

Tap **Component Match** to scan your app and see all tagged targets. Select one, and the overlay pins to that component — measuring its exact position and size. When the component scrolls or repositions, the overlay follows automatically.

### Free Mode

Manually position and resize the overlay anywhere on screen. Drag to move, pinch to scale — useful when you want to compare a full-screen mockup or a section that doesn't map to a single component.

---

## What You Can Do

- **Opacity control** — Blend the mockup over your live UI to spot differences
- **Scale & zoom** — Resize the overlay to match your layout
- **X/Y offset** — Fine-tune positioning for exact alignment
- **Flip** — Mirror the overlay horizontally or vertically
- **Lock** — Prevent accidental repositioning while comparing
- **Outline toggle** — Show overlay boundaries for precise placement
- **Auto-track** — Remeasures the target component on every render cycle so the overlay stays locked even during animations and layout shifts

---

## What's Next

- [Highlight Updates](./highlight-updates) — See exactly why components re-render
- [Environment Inspector](./env) — View and search environment variables
- [Network Monitor](./network) — See every API call your app makes
