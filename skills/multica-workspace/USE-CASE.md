# multica-workspace

**Pack:** bot-company-os

## Use case

'Use when managing Multica issues via CLI.'

## How to use it

## Procedure

1. Resolve workspace: `multica workspace list --output json`, use the UUID. Cached: Darren111 = `2f389948-dcd4-446a-8fcb-1472b870a531` (verified 2026-09-07) — reuse directly, re-list only if calls start failing.
2. List issues paged: `multica issue list --output json --limit 100 --offset N` until `has_more` is false. Default list caps at 50 and hides closed work.
3. Find full scope: `multica issue search <term> --include-closed --output json` plus `multica issue list --project <project-uuid> --output json`. Title-only filtering misses project members whose title lacks the keyword.
4. Change state with `multica issue status <uuid> <key> --no-start` (keys: todo, in_progress, in_review, done, blocked, cancelled). Always `--no-start` unless an agent run is intended.
5. Verify every write: `multica issue get <uuid>` or re-list before reporting. Never claim tracking succeeded without read-back.
6. Track each substantive request as its own issue (title, objective, status, next action), set to done on delivery, verify by read-back. Create with `multica issue create --workspace-id <uuid> --title "..." --description "Objective: ... Status: ... Next: ..." --status todo --output json`; when the profile default is already Darren111 omit the workspace flag entirely — `issue create` accepts `--workspace-id` only, never bare `--workspace`. a distinct multi-step outcome also gets `multica project create --workspace-id <uuid> --title "..." --description "..." --output json`. Post progress notes with `multica issue comment add <uuid> --content "..."` — the subcommand is `add`, not `create`. `multica issue update <IDENTIFIER> --status done --description "..."` also accepts the human key (`DARREN-123`), not only the UUID.
7. Report in Darren format: what changed / verified / left. Lead with counts, then active items with next action. No process replay. Format for Telegram: airy and attractive — short header, blank lines between sections, max 4-6 bullets per section, bold section labels, live links on their own lines. Never send a dense wall of counts. End blocked/todo reports with a clarify question offering the next actions as tappable choices (recommended first) instead of plain 'reply 1' text. Deliver results as native chat attachments or playable media — never md/doc files; Darren consumes in chat, not in editors.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/multica-workspace/`). Video walkthrough ships with the pack.
