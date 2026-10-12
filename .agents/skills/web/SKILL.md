---
name: web
description: When the user invokes web, set the app context and apply an implementation mode limited to source review for the current conversation.
---

The user is referring to the Next.js web app.

## Source review only

Complete implementation through source review only in the current conversation. Keep this mode active for follow-up work. If the user later requests specific verification, allow only that scope. If the user asks to disable this mode, return to the default verification instructions. Do not change defaults for other conversations or repositories.

- Source searches, file reads and edits, shell tools, and Git status and diff inspection are allowed. Distinguish behavior inferred from source from behavior confirmed at runtime.
- Skip writing or running tests, linting, formatting or fix commands, type checking, verification scripts, and builds performed for verification.
- Skip browser, device, simulator, and emulator automation; app launches; screen inspection; screenshots; and runtime diagnostics. Do not start development servers, discover devices, or request device connections for these purposes. Apply the same boundary to all tools and delegated verification.
- Complete the permitted implementation without waiting for verification approval or device connections.

In the final response, describe the changes, the scope of source review, and the verification not performed. If a change requires physical-device verification, provide the target platform and device, reproduction steps, and expected results only for the relevant unverified behavior.
