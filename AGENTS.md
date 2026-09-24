# Buoy setup notes for coding agents

Buoy provides in-app tools for React Native and Expo. Use the complete [installation guide](https://buoy.gg/install.md) to select packages, configure account access, and verify capture. Follow the user's instructions and the target repository's constraints.

## Setup requirements

- Install core and the selected tool packages in the app workspace with its existing package manager. Check compatible versions and peer dependencies.
- A Free or Pro account key is required. Use `npx --package=@buoy-gg/core buoy login` when sign-in is authorized, or reuse the app's configuration. Do not print keys.
- Expo uses `Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY })`. React Native CLI needs its own environment loader; writing `.env.local` alone does not configure the app.
- Mount `FloatingDevTools` inside the existing providers. Register stores, atoms, storage backends, or tool callbacks as required. Avoid creating duplicate providers or stores for inspection.
- Start with development-only access. Preserve existing authorized behavior; do not add production visibility or release sync without the user's authorization. Some tools remain development-only regardless of plan.

## Connections and platforms

React Native Desktop/MCP connections require `@buoy-gg/external-sync`. Desktop and the MCP process need their own account setup. MCP data and action tools require Pro. A reachable broker does not prove account admission or app authorization.

Use the [Flutter installation guide](https://buoy.gg/buoy/latest/docs/flutter/installation) for Flutter's account-configured, debug-only widget and explicit app integrations. Native Swift has a separate [package guide](https://github.com/Buoy-gg/Buoy-Swift). TV requires the [TV setup](https://buoy.gg/buoy/latest/docs/tv/installation) and supported host tools. Do not assume capability parity across platforms.

## Verify and report

Check dependency resolution and the app's existing static checks. Then trigger a known request after account access and inspect it in Network. State whether device or remote checks were actually performed. A package appearing in the menu does not establish that its data source is connected.

Account validation and configured connections use the network. See [Telemetry](https://buoy.gg/buoy/latest/docs/telemetry) for data flows.

## Documentation formats

- [Human documentation](https://buoy.gg/buoy/latest/docs/overview)
- [Agent installation guide](https://buoy.gg/install.md)
- [Curated index](https://buoy.gg/llms.txt)
- [Combined documentation](https://buoy.gg/llms-full.txt)

Append `.md` to a docs page URL for its Markdown version. Interactive content may link back to the visual page; use the tool guide when an example or integration needs more context.
