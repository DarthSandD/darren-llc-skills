---
name: large-dir-delete
description: Delete large dirs in background; foreground rm -rf stalls.
---

# Large recursive deletion

## Why this skill exists

Recursive deletes of large directories (Gradle caches, build trees, node_modules, pip caches) routinely exceed the foreground terminal timeout even when the actual I/O is fast. The right move is not to re-run with a bigger timeout — it is to hand the job to a background process and verify by reading filesystem state afterward.

## Decision: foreground vs background

| Signal | Action |
|---|---|
| Directory is small (du -sh returns fast, under a few hundred MB) | Foreground rm -rf, normal timeout |
| du -sh itself is slow / floods stderr with "cannot access" for thousands of paths, or rm -rf stalls past ~60s | Background rm -rf with notify=true, then verify |

A slow `du -sh` is itself a signal that the tree is too large for a synchronous delete — do not keep re-running foreground.

## Workflow

### 1. Measure first (optional but recommended)

```bash
du -sh "$DIR"       # rough size — if this drags, skip to background
ls -la "$DIR"        # top-level shape, confirms target exists
```

### 2. Delete

**Small / fast:**

```bash
rm -rf "$DIR" && echo "OK" || echo "FAIL"
```

**Large / slow (foreground times out):**

```bash
# From terminal, background=true, notify=true
rm -rf "$DIR" && echo "DELETED-OK"
ls -la "$DIR" 2>&1 || echo "CONFIRMED-GONE"
```

- `notify=true` fires one notification on exit so you do not have to poll.
- Do NOT chain a long `du -sh` after the delete in the same call — `du` on a freshly-deleted tree often produces thousands of "cannot access" lines (the deleted paths are already gone when du walks them) and adds no signal.
- Do NOT poll with `process_manage wait` unless you need the exit code synchronously; `notify=true` is cheaper.

### 3. Verify

```bash
ls -la "$DIR" 2>&1   # should report "No such file or directory"
```

A clean "No such file or directory" from `ls` on the top-level path is enough verification. Do not re-run `du` to prove the subtree is gone.

### 4. If the directory partially survived

If `ls` shows the directory still exists but is much smaller, the delete may have been interrupted mid-run (e.g. approval gating). Re-run the same background delete once. Do not hand-delete sub-trees one at a time.

## Pitfalls

- **Foreground rm -rf on a multi-hundred-MB tree → timeout → re-run with bigger timeout.** Wrong. The delete is already doing the work; the terminal just cannot wait. Switch to background, do not inflate the timeout.
- **Chasing du output after a delete.** `du` on a half-deleted tree floods stderr with "cannot access" for every already-removed path and adds noise, not signal. Trust `ls` on the top-level path.
- **Partial survives due to smart-approval gating.** On Windows, recursive deletes of large trees can be auto-approved by the runner and still take real time. A timeout does not mean failure — check with `ls` before concluding.
- **Deleting the wrong tree because the path was guessed.** Always `ls` the target first. If the expected project dir does not exist at the remembered path, report it rather than searching the whole home tree for a replacement — the user may have moved or renamed it.
- **'Device or resource busy' on rm -rf.** A backgrounded command pins the session cwd — if any prior command `cd`'d into (or under) the target, the OS holds the handle and the delete fails. `cd` back out first, then re-run once; do not escalate to per-file deletes.

## What not to capture

- Do not create a skill per-project (e.g. "delete jarvis gradle"). The pattern is the same for any large tree.
- Do not record the size of the deleted tree or the exact free-space outcome as a rule — those are incident details, not procedure.
