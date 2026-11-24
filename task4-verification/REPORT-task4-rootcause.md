# task4: why Gaten's accessibility service "would not stay bound" on the moto g35 (Android 15)

Build under test: origin/dev arm64 release signed with the local debug key (before-arm64-signed.apk, sha256 c1d78c4a...).
Verdict: environment, not an app bug. No code change.

1. `dumpsys accessibility` showed `Enabled services:{Gaten}` but `Bound services:{}` and no Binding/Crashed entries, and logcat had no
   Gaten crash. The driver (agent-device) starts `com.callstack.agentdevice.snapshothelper`, a UiAutomation instrumentation, on
   every `snapshot`, `swipe` and `gesture`. While a UiAutomation connection exists Android suppresses all third-party accessibility services.
   `am force-stop com.callstack.agentdevice.snapshothelper` -> Gaten is bound immediately (Lookup mode "On", floating button appears).
   A later `snapshot` / `swipe` / `gesture pan` unbinds it again. `press` and `screenshot` do not start the helper.
2. "Reverts to empty after a restart": `am force-stop com.gaten` always clears `enabled_accessibility_services` (stock Android behaviour).
3. Restricted settings (ACCESS_RESTRICTED_SETTINGS=allow) was not the blocker; Allow worked.

Part B (drag the red X): NOT completed. Every drag primitive allowed here (`swipe`, `gesture pan`) starts the helper, which unbinds the
service and destroys the overlay mid-gesture, and a coordinate `press` cannot drag. adb input is not allowed by the brief.
Evidence: lookup-flow-working.mp4/.gif (bubble -> lookup with red X at the bubble's position -> closes -> bubble back),
drag-attempt-blocked.mp4 (the scripted attempt; once the helper starts, taps fall through to the app behind).
Needed to finish: a drag path that doesn't use UiAutomation (e.g. owner-approved `adb shell input swipe` with a hold), or run the driver with
UiAutomation started with FLAG_DONT_SUPPRESS_ACCESSIBILITY_SERVICES.
