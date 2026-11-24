# Task 4: floating X drag + position memory: BLOCKED (not verified)

Device: moto g35 5G (ZA222WGJPP), Android 15. Build: BEFORE APK origin/dev 91aa687,
arm64-v8a, signed with local debug key, sha256 c1d78c4a21a9f963b4f186402c12366bc8cce3983f941e473d63152c1a584178.

Result: no lookup, X, or drag attempts were possible, so 0 of 5 attempts were run.

What blocked it:
- Gaten's lookup mode needs its accessibility service. The service was enabled in
  Settings > Accessibility > Gaten > "Use Gaten" (Allow). `settings get secure enabled_accessibility_services`
  returned `com.gaten/com.gaten.app.GatenAccessibilityService` right after Allow.
- It did not stay enabled: the setting reverted to `null` after a Gaten process restart / force-stop, and
  `dumpsys accessibility` showed "Bound services:{}" even while enabled.
- The Lookup mode switch in Gaten keeps showing "Off. Needs Android's accessibility permission.", and
  toggling it shows the disclosure again.
- No Gaten crash was seen in logcat; the only Gaten-related lines were the app's MainActivity death on restart.

Screenshots: blocked-lookup-settings.png (Lookup mode still Off), blocked-accessibility-gaten-off.png
(Accessibility list shows Gaten Off).

Verdict: Works as asked: not verified (blocked by accessibility service not staying enabled/bound on this device).
