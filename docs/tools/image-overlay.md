---
title: Image Overlay
seoTitle: "React Native Design Overlay — pixel-perfect UI against mockups"
id: tools-image-overlay
description: "Overlay design mockups on your running React Native app and tweak opacity, scale, and position to nail pixel-perfect UI without leaving the simulator."
---

<!-- ::platform-badge platform="both" -->

Overlay design mockups directly on your running app to compare against your implementation. Load a mockup, match its scale and position to the screen, and adjust opacity to compare spacing and alignment.

The demo shows component targeting and alignment on a mock storefront.

<!-- ::image-overlay-live-demo -->

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
  <Button title="Sign In" onPress={() => {}} />
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

Clipboard image loading requires `expo-clipboard` and its native setup. Use a direct image URL for the minimal path; load it, reduce opacity, align the mockup, and lock the overlay before interacting with the screen.

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
- [Network Monitor](./network) — Inspect supported HTTP requests

---

## FAQ

### How do I compare my React Native UI against a Figma design?

Export the frame as an image, then load it in Image Overlay from clipboard or URL — adjust opacity over the running app and differences jump out.

## Web support (unreleased)

Tag browser targets with data-testid="image-target:Name" and import the browser registration before React DOM. The shared controls support target and free placement. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
