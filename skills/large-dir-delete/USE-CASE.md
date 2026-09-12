# large-dir-delete

**Pack:** system-utils

## Use case

Delete large dirs in background; foreground rm -rf stalls.

## How to use it

Follow the skill body step by step; verify with tool output before handover.

## Pitfalls

- **Foreground rm -rf on a multi-hundred-MB tree → timeout → re-run with bigger timeout.** Wrong. The delete is already doing the work; the terminal just cannot wait. Switch to background, do not inflate the timeout.
- **Chasing du output after a delete.** `du` on a half-deleted tree floods stderr with "cannot access" for every already-removed path and adds noise, not signal. Trust `ls` on the top-level path.
- **Partial survives due to smart-approval gating.** On Windows, recursive deletes of large trees can be auto-approved by the runner and still take real time. A timeout does not mean failure — check with `ls` before concluding.
- **Deleting the wrong tree because the path was guessed.** Always `ls` the target first. If the expected project dir does not exist at the remembered path, report it rather than searching the whole home tree for a replacement — the user may have moved or renamed it.
- **'Device or resource busy' on rm -rf.** A backgrounded command pins the session cwd — if any prior command `cd`'d into (or under) the target, the OS holds the handle and the delete fails. `cd` back out first, then re-run once; do not escalate to per-file deletes.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/large-dir-delete/`). Video walkthrough ships with the pack.
