---
name: dotnet-desktop-ship
description: 'Use when shipping .NET Windows desktop apps.'
---

# .NET Desktop Ship

Build, verify, and publish .NET (WPF/WinForms) Windows desktop apps whose value lives in native dependencies (OCR, PDF, imaging, CAD). The packaged artifact is the product — bin output passing tests proves nothing about the ship.

## Procedure

1. Build Release, zero warnings. Run the full suite.
2. Publish the shippable shape (loose self-contained folder by default — see single-file rule), then smoke-test the PUBLISHED artifact, not bin: launch it cold, drive it via the `windows-uia-verify` workflow, and keep reusable probe scripts in the repo's own `tools/` directory.
3. Stamp Version/AssemblyVersion/FileVersion in the csproj before the final publish; confirm the stamp on the shipped exe afterwards.
4. Update README (correct publish path, real troubleshooting), CHANGELOG, and a live screenshot of the shipped build on every release.

## Rules

- Never ship single-file when any dependency resolves natives via Assembly.Location — it is empty in single-file bundles, so loaders built on it (e.g. Tesseract 5.x InteropDotNet) die at startup while bin-folder runs stay green; lock the decision in the csproj with a comment so nobody re-breaks it.
- Never use AppDomain.BaseDirectory or Assembly.Location for runtime paths in a shippable app — use AppContext.BaseDirectory with an executable-directory fallback, since single-file and hosted contexts null out the others.
- Regression tests must drive the app's real stage sequence with realistic inputs (production DPI, real files, small fonts) — fixtures that feed clean synthetic data straight into one stage stay green while the shipped path is broken.
- Never code against a guessed NuGet API — read the `.xml` docs shipped beside the DLL under `~/.nuget/packages/<pkg>/<ver>/lib/<tfm>/` first; when reflection dumps fail with ReflectionTypeLoadException, read the loadable subset, falling back to a strings scan of the DLL for method names.
- Surface dependency init failures in the app's own log with the full inner-exception chain — a bare 'engine unavailable' status hides packaging bugs that only exist in the shipped layout.
- Stop the running app before publish or zip — the OS locks the exe and loaded DLLs, so packaging over a live process fails (MSB4018 on publish, UnauthorizedAccess on zip); kill, confirm exit, then package, then relaunch.
- Generate synthetic test fixtures in-repo — never commit a user's personal file as a test asset no matter how convenient; a hand-rolled minimal file plus an assertion on the property under test is enough.
