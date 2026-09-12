---
name: windows-uia-verify
description: 'Use when driving Windows desktop apps via UI Automation.'
---

# Windows UIA Verify

Drive native Windows desktop apps (WPF, WinForms, Win32) through the UI Automation tree from PowerShell when pixel/coordinate automation stalls. Deterministic — no scaling math — and every read doubles as verification.

## Procedure

1. Confirm a real window first: `MainWindowHandle` nonzero. A slow cold start (e.g. single-file self-extraction) shows handle 0 for many seconds — wait, never automate a window that does not exist yet. UIA needs the interactive desktop; a service session with no windows means stop, not retry.
2. Run everything from `.ps1` files (`powershell -NoProfile -ExecutionPolicy Bypass -File <script>`). Never inline `-Command`: the shell bridge mangles `$` variables and quoting.
3. Persist probe scripts under the project's own `tools/` directory — never %TEMP%, which is volatile and gets wiped mid-session along with your probes.
4. When the user asks to SEE or touch the app themselves, raise it: ShowWindow + SetForegroundWindow from a `.ps1` (background-first routing does not apply — the user requested visibility).
5. Actuate by pattern: buttons via InvokePattern, ComboBoxes via ExpandCollapsePattern, text via ValuePattern/Text, logs via ScrollPattern. Re-read after each actuation; the read IS the verification.

## Rules

- WPF ComboBox items surface as type names (DisplayMemberPath is invisible to UIA) — verify selection via the app's own log/status text, never the UIA item name.
- Virtualized lists expose only realized rows, so ScrollPattern to bottom before reading a log; the status-bar text is ground truth when the visible log looks frozen.
- Element-index clicks refused plus foreground coordinate clicks that report delivered with no visible effect is the signal to switch to UIA — do not loop the pixel ladder against it.
- A window that is blank-white yet responding, with a full UIA tree and zero matching Application event-log errors, is a compositor/render failure, not app logic — prove the logic stays alive via UIA actuation, then relaunch once for the transient DWM/GPU glitch before changing any code.
- A custom ComboBox template that does not forward SelectionBoxItemTemplate (and ItemTemplateSelector) silently disables DisplayMemberPath, so items render as type names — and override ToString() on the item model regardless, since UIA and fallback paths use it.
- SelectionItemPattern.Select() throwing 'Unsupported Pattern' on a dropdown item is a provider limitation, not an app bug — fall back to the app's own default/auto-pick path or keyboard selection instead of retrying the pattern.
