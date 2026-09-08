# Verify a migrated screen against the running web app

Reference for [`expo-web-to-native`](../SKILL.md), steps 3–4. Compare the running native screen with the web original for the same route and data. Compilation alone does not prove that either screen renders correctly.

## Setup

Use Argent MCP for both browser and React Native automation, testing, and debugging. Read [`argent-device-interact`](../../argent-device-interact/SKILL.md) and, for native startup, [`argent-react-native-app-workflow`](../../argent-react-native-app-workflow/SKILL.md). Follow the Argent availability guidance if its tools are unavailable.

Call `list-devices` before selecting a target. Use a Chromium CDP target for the web app. Open or drive a simulator only when the current user message explicitly requests simulator use, as required by the repository guardrail. Otherwise, complete the web check and report native verification as pending.

For saved paths or regression tests, read `argent-create-flow` or `argent-qa-flows` and start recording before the first action.

## Workflow

1. Run the web app using the repository startup command, or use the supplied deployed URL. On the Chromium target, use `open-url` for the route, `describe` for structure, and `screenshot` for the visual reference. Capture a baseline for each screen.
2. When native verification is authorized, follow the appropriate Argent setup skill and the project's build/start commands. Use `launch-app` or `open-url` to reach the matching native route. Inspect React Native structure with `debugger-component-tree`, falling back to `describe`, and capture the screen with `screenshot`.
3. Compare content, data, route parameters, and interactions. A DOM-shelled screen should retain the web UI; a nativized screen should use native controls and navigation while preserving behavior.
4. Check transitions and gestures using the relevant Argent interaction tools. For video evidence, use `argent-screen-recording`; for replayable paths, use `argent-create-flow`. Take interaction coordinates from the current element tree.
5. Report observed behavior, evidence, and any unverified platform. Stop only this session's device services with `stop-all-simulator-servers` and an explicit `devices` list.

## Pass criteria

- **DOM-shelled screen:** the web UI renders within the native shell, and native route parameters produce the same result as on the web.
- **Nativized screen:** native primitives replace the webview while preserving the original content and behavior. Inspect motion as well as still screenshots; see `native-patterns.md` for native interaction guidance.
