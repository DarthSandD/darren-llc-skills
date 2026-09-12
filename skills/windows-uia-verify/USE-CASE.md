# windows-uia-verify

**Pack:** ship-deploy

## Use case

'Use when driving Windows desktop apps via UI Automation.'

## How to use it

## Procedure

1. Confirm a real window first: `MainWindowHandle` nonzero. A slow cold start (e.g. single-file self-extraction) shows handle 0 for many seconds — wait, never automate a window that does not exist yet. UIA needs the interactive desktop; a service session with no windows means stop, not retry.
2. Run everything from `.ps1` files (`powershell -NoProfile -ExecutionPolicy Bypass -File <script>`). Never inline `-Command`: the shell bridge mangles `$` variables and quoting.
3. Persist probe scripts under the project's own `tools/` directory — never %TEMP%, which is volatile and gets wiped mid-session along with your probes.
4. When the user asks to SEE or touch the app themselves, raise it: ShowWindow + SetForegroundWindow from a `.ps1` (background-first routing does not apply — the user requested visibility).
5. Actuate by pattern: buttons via InvokePattern, ComboBoxes via ExpandCollapsePattern, text via ValuePattern/Text, logs via ScrollPattern. Re-read after each actuation; the read IS the verification.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/windows-uia-verify/`). Video walkthrough ships with the pack.
