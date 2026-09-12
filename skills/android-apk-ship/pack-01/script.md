# Skill Video 01 — android-apk-ship (~85s, ELI5, hook-first)

## HOOK (12s)
Gradle just told you BUILD SUCCESSFUL. Congrats — it might be lying. Your APK could be yesterday's classes in a new wrapper. This skill makes the APK prove itself. Three checks.

## STEP 1 — Toolchain (25s)
Check one: the toolchain. Java 17 for AGP 8 — never override JAVA_HOME blindly, one wrong export breaks every build after it. Build with the repo's own Gradle launcher, in background with a long timeout. Foreground builds stall your whole session.

## STEP 2 — Ground truth (30s)
Check two: ground truth. After any big rewrite, clean first — Gradle serves stale classes as UP-TO-DATE and the log lies. Then verify: aapt dump badging for package and launcher, unzip the dex and grep your new classes present and dead ones absent, apksigner for release. Sizes in bytes. That's the only proof a rewrite shipped.

## STEP 3 — Handover (20s)
Check three: the handover. Crash report? Hash first, debug second — mismatch means corrupt transfer, match means real bug. Keep keystores out of git. Update the README, comment the issue, report APK paths plus sizes first. Done — the APK is proven, not promised.
