---
name: desktop-app-verification
description: 'Use when smoke-testing a native desktop app build.'
---

# Desktop App Verification

A native desktop build is not proven by a clean compile. Prove the window appears
before claiming it works, cheapest checks first.

## Procedure

1. Locate the real launch binary first (e.g. `publish/<App>.exe`), and confirm it exists
   on disk before launching anything.
2. Launch via `terminal(background=true)` with the native path — backgrounding is a tool
   flag, never an appended `&` in a foreground command.
3. Confirm liveness with `process_manage(action="poll")` — a running PID proves process
   start even when no window appears.
4. Capture the app: `computer_use(action="capture", app="<ExeName>", mode="som")`.
   On `no on-screen window`, take one `capture(app="screen", mode="vision")` as the
   no-window proof before any retry.
5. When the process runs but no window appears on Windows, treat Session 0 isolation
   as the likely cause (a service session has no interactive desktop) — stop climbing
   the input ladder there, because no rung can click a window that is on no desktop.
6. Fall back to code-level proof in the same run (solution build + full test suite),
   then kill the background process with `process_manage(action="kill")` so no orphan
   GUI process lingers.
7. Report the two levels separately: process/tests versus GUI click-through. A passing
   test suite never counts as a passed GUI test.

## Pitfalls

- Kill the launched process after every smoke test — an orphaned app holds files and
  ports, so the next launch or rebuild fails for reasons unrelated to the code.
- Take the full-screen capture before concluding "no window" — an app-scoped capture
  alone cannot distinguish a hidden window from a wrong app name.
- Report GUI verification as failed when the window never appeared — claiming a
  click-through from build output alone misleads the next session into shipping
  untested UI.
