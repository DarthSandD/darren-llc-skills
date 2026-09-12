---
name: windows-gui-verification
description: 'Use when verifying a Windows desktop GUI app end-to-end.'
---

# Windows GUI Verification

Prove a Windows desktop app works by driving the real window and reading its state back — never by build-success alone. Pixel screenshots are evidence; UI Automation (UIA) is control.

## Procedure

1. Launch the exact artifact under test and confirm a live main window: `Get-Process <Name> | Format-List Id,MainWindowHandle,MainWindowTitle` — HWND `0` means still starting; wait and re-check.
2. Drive controls with UIA `InvokePattern`/`ExpandCollapsePattern` from a `.ps1` file (see `references/uia-probes.md`), not with pixel clicks: element-index clicks go stale between captures and coordinate clicks need per-capture native-space scaling that silently misses.
3. Assert state by reading it back: log-list items, text values, status-bar text. A click that returns 'unverifiable' means re-capture and read — never re-click blind.
4. Verify the SHIPPED artifact (published/installed layout), not the build tree: native-library loaders can behave differently per layout (single-file bundles leave `Assembly.Location` empty, which breaks loaders that resolve natives relative to it).
5. Kill the running exe before republishing over it, or the bundler fails with an MSB4018 file-lock error.
6. Keep evidence: persist UIA probe scripts in the project's `tools/` dir and save one screenshot of the working app under the project's `screenshots/`.

## Pitfalls

- WPF `ListBox` virtualizes: UIA only exposes realized rows, so newest log lines are invisible until you scroll — drive `ScrollPattern.SetScrollPercent(-1, 100)` to the bottom before reading, and cross-check the status-bar text, which always reflects current state.
- A background capture taken right after bring-to-front can show a stale blank frame while UIA already sees the full tree — the app is fine and the on-screen window paints normally; re-capture once before concluding anything about rendering.
- Background mouse input is refused for occluded windows (`background_occluded`) and foreground clicks need the window actually foreground — UIA `InvokePattern` sidesteps the whole ladder for standard controls, so reach for it first on WPF/WinForms apps.
- An app that enumerates its own windows (window pickers, capture tools) must be launched in the interactive session (Session 1), not the service session (Session 0) — in Session 0 the window exists but neither the driver nor the app's own enumeration sees anything usable.
