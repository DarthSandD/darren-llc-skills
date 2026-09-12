---
name: hermes-restore
description: "Use when restoring Hermes after a wipe or migration."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows, linux, macos]
---

# Hermes Restore

1:1 recovery of a Hermes Agent install after a wipe, reinstall, or hardware move, without a backup tarball. Restore from the migration prompt / on-disk backups, then verify every layer with read-backs.

## Procedure

1. **Survey live state first** — `hermes --version`, `hermes doctor`, `hermes config check`, `hermes cron list`, `hermes skills list`, `hermes mcp list`, `hermes gateway status`. Record what is actually missing before touching anything.
2. **Back up the current state** — copy `config.yaml` to a timestamped backup before any restore step, so a bad restore is reversible.
3. **Restore plain files first** — `IDENTITY.md`, `SOUL.md`, `channel_directory.json` via file write; these are safe and never corrupt the gateway.
4. **Fix config via CLI only** — run `hermes config migrate` for version drift, then apply settings with `hermes config set section.key value`. Never hand-edit `config.yaml` — a stray indent corrupts the file and breaks the live gateway.
5. **Restore MCP servers via config, not the wizard** — set `mcp_servers.<name>.url/auth/enabled` with `hermes config set`, then confirm with `hermes mcp list`.
6. **Recreate profile dirs and scripts dir**, then recover real scripts from outside `HERMES_HOME` (home dir, repos) before writing stubs.
7. **Recreate cron jobs from the spec table** (name, schedule, script/mode, deliver target) with `hermes cron create`; confirm count with `hermes cron list`.
8. **Restart the gateway and fix messaging auth** — `hermes gateway restart`, then read `logs/gateway.log` for the platform adapter verdict.
9. **Log the work on the tracking issue** and read it back; then run a fresh `hermes-migrate.sh backup` so the loss cannot recur.
10. **Answer "update for current job" by resolving then verifying** — list in-progress issues sorted by most-recent update and treat the top one as current; verify live layers (config model keys, profile dir count, script file count, cron job count, gateway state pid + platform connection, todo list) and report in changed / verified / left shape.

## Pitfalls

- Resolve the current job by recency across in-progress tracking issues — asking the user which job wastes a round-trip when the freshest update already identifies it.
- Report restore status as changed / verified / left with live counts — summaries without read-back numbers hide regressions.
- Pass the full issue UUID and `--content` when adding a Multica comment — short workspace prefixes are rejected as invalid and `--body` is an unknown flag.
- Never run `hermes mcp add` non-interactively — it blocks on OAuth flows and confirmation prompts until timeout; set the `mcp_servers.*` keys via `config set` instead.
- `hermes config list` does not exist — use `config show` or `config get`; scripting the wrong subcommand wastes a whole round-trip.
- Telegram is fail-closed: with no allowlist configured it denies everyone including the home user — set `TELEGRAM_ALLOWED_USERS` in `.env` and restart the gateway.
- A reinstall wipes sessions, memories, cron, and logs but project files outside `HERMES_HOME` usually survive — survey the home directory before declaring work lost.
- Mark unrecoverable script/job bodies as documented STUBs with their origin — fabricated logic ships as trusted code and fails silently on schedule.
- Verify with read-backs (`mcp list`, `cron list`, `gateway status`, issue get) — a successful write call is not proof the system absorbed it.
