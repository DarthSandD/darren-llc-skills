# native-app-verification

**Pack:** ship-deploy

## Use case

'Use when verifying native GUI apps.'

## How to use it

## Procedure

1. **Confirm the window exists in YOUR session.** A running PID proves
   nothing about visibility: check `MainWindowHandle` (non-zero) and
   the process session id. A process in a non-interactive/service
   session has no visible window no matter how healthy it is — relaunch
   it into the interactive session and re-check before concluding the
   app failed to start.
2. **Enumerate controls by name, not coordinates.** Find buttons, text
   boxes, dropdowns, and status elements via the accessibility tree
   (Windows: PowerShell `UIAutomationClient` — `AutomationElement.FromHandle`,
   match `NameProperty` + `ControlTypeProperty`).
3. **Act through control patterns.** Press buttons with InvokePattern,
   read text with ValuePattern / Text elements, open dropdowns with
   ExpandCollapsePattern. Allow real settle time after each Invoke —
   captures, enumerations, and recognizers are async, and reading too
   early mimics a frozen app.
4. **Assert on values, cross-check the channel.** Read the status bar,
   the result control, and the log; if they disagree, the observation
   channel is suspect, not the app (see pitfalls). Check process CPU
   across two samples: idle + no new output means finished-or-dead —
   the status text tells which.

## Pitfalls

- Never trust a verdict-only health check: a boolean available/ready
  flag that swallows its exception hides the root cause — surface the
  captured error (retain as LastError, log at the decision point) WHY
  silent false values send every retry down the wrong path.
- Never trust a virtualized list's item count: realized items only are
  exposed, so new entries exist while the tree looks frozen — confirm
  state via the status bar or control values WHY the tree is a viewport,
  not the backing store.
- Never trust a control's accessibility name for rendered text: custom
  templates can expose type names while the screen shows correct values
  — confirm visually via screenshot WHY providers and renderers differ.
- Never force SelectionItemPattern when the control reports it
  unsupported — fall back to the app's default-selection behavior WHY
  custom combo items often omit the selection provider.
- Never conclude from one pixel-click round: coordinate scaling and
  occlusion make single clicks ambiguous — one delivered-but-unverified
  click is evidence of nothing; re-read state before retrying WHY input
  delivery and UI effect are separate events.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/native-app-verification/`). Video walkthrough ships with the pack.
