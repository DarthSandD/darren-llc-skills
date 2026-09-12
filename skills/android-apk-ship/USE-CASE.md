# android-apk-ship

**Pack:** ship-deploy

## Use case

Use when building, verifying, or handing over Android APKs.

## How to use it

## Procedure

1. **Confirm toolchain before building.** Run `java -version` (need 17 for
   AGP 8.x) and use the repo's known-good Gradle launcher. Never override
   `JAVA_HOME` blindly — a wrong export breaks every subsequent build.
   Completion: `gradle --version` prints under the right JDK.
2. **Build in background with a long timeout** (`assembleDebug`, then
   `assembleRelease`). Foreground builds stall the session.
3. **After any large rewrite, `clean` first.** Gradle serves stale
   compilation units as UP-TO-DATE / FROM-CACHE and reports BUILD
   SUCCESSFUL on yesterday's classes — the log lies, the dex does not.
   Completion: full task list executes, not cached.
4. **Verify ground truth, in this order:**
   - `aapt dump badging <apk>` → package, versionCode/versionName, permissions.
   - `aapt dump badging | grep launchable-activity` → the right launcher.
   - Unzip `classes*.dex`, grep for your new classes present AND dead
     classes absent — this is the only proof a rewrite actually shipped.
   - Release only: `apksigner verify --print-certs` → expected signer DN.
   - Record both file sizes in bytes.
5. **User reports a crash? Hash first, debug second.** `sha256sum` the
   returned file against the local build: mismatch means transfer
   corruption (resend), match means a real code crash (restart/fix).
   Never start a fix hunt before this check.
6. **Secret hygiene before commit.** `git diff --cached --name-only` must
   show no `*.keystore`, `.keystore-pass`, or local `gradle.properties`.
   Commit, push, verify the push landed.
7. **Finish the job in the same task:** update the repo README (features,
   arch, build/verify steps, APK sizes), comment the Multica issue, report.

## Pitfalls

- Trust `UP-TO-DATE` after deleting/adding dozens of files — stale build
  cache keys on old snapshots, so force `clean` and re-verify the dex.
- Ship an APK verified only by "BUILD SUCCESSFUL" — version and launcher
  come from `aapt`, signature from `apksigner`, contents from the dex.
- Debug a user-reported crash without hash-comparing the file — a corrupt
  download and a code bug demand opposite responses.
- Commit signing secrets alongside the release — the keystore and local
  `gradle.properties` stay untracked; check the staged list every time.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/android-apk-ship/`). Video walkthrough ships with the pack.
