---
name: multica-workspace
description: 'Use when managing Multica issues via CLI.'
---

# Multica Workspace Management

Default workspace is Darren111 (UUID `2f389948-dcd4-446a-8fcb-1472b870a531`). Pass explicit UUIDs, never slugs, for issue/project ops — `--workspace-id Darren111` (slug) fails with `Invalid request: invalid workspace_id` (hit twice on 2026-09-07).

## Procedure

1. Resolve workspace: `multica workspace list --output json`, use the UUID. Cached: Darren111 = `2f389948-dcd4-446a-8fcb-1472b870a531` (verified 2026-09-07) — reuse directly, re-list only if calls start failing.
2. List issues paged: `multica issue list --output json --limit 100 --offset N` until `has_more` is false. Default list caps at 50 and hides closed work.
3. Find full scope: `multica issue search <term> --include-closed --output json` plus `multica issue list --project <project-uuid> --output json`. Title-only filtering misses project members whose title lacks the keyword.
4. Change state with `multica issue status <uuid> <key> --no-start` (keys: todo, in_progress, in_review, done, blocked, cancelled). Always `--no-start` unless an agent run is intended.
5. Verify every write: `multica issue get <uuid>` or re-list before reporting. Never claim tracking succeeded without read-back.
6. Track each substantive request as its own issue (title, objective, status, next action), set to done on delivery, verify by read-back. Create with `multica issue create --workspace-id <uuid> --title "..." --description "Objective: ... Status: ... Next: ..." --status todo --output json`; when the profile default is already Darren111 omit the workspace flag entirely — `issue create` accepts `--workspace-id` only, never bare `--workspace`. a distinct multi-step outcome also gets `multica project create --workspace-id <uuid> --title "..." --description "..." --output json`. Post progress notes with `multica issue comment add <uuid> --content "..."` — the subcommand is `add`, not `create`. `multica issue update <IDENTIFIER> --status done --description "..."` also accepts the human key (`DARREN-123`), not only the UUID.
7. Report in Darren format: what changed / verified / left. Lead with counts, then active items with next action. No process replay. Format for Telegram: airy and attractive — short header, blank lines between sections, max 4-6 bullets per section, bold section labels, live links on their own lines. Never send a dense wall of counts. End blocked/todo reports with a clarify question offering the next actions as tappable choices (recommended first) instead of plain 'reply 1' text. Deliver results as native chat attachments or playable media — never md/doc files; Darren consumes in chat, not in editors.

## Rules

- Multica has no issue-delete endpoint; retraction means `status cancelled`, which removes the issue from the active board — deleting a project (`multica project delete <uuid>`) does not delete its issues, so cancel issues first.
- Destructive scopes (cancel-many, project delete, disk rm) require explicit user confirmation via a scope-inventory prompt naming exact issue IDs, project UUID, paths, and sizes before executing.
- Search without `--include-closed` hides done/cancelled issues and undercounts the scope; always use it for removal or audit sweeps.
- Before keeping an issue blocked pending auth, verify the live connection first via Composio SEARCH_TOOLS connection status plus one minimal read — an ACTIVE connection with a successful read means the blocker is already cleared, so close it instead of asking for another login.
- Issues that only mention a keyword in a comment match `issue search` but are not project members; exclude them from bulk scope unless the user confirms.
- Live-sync: every substantive Telegram/PC/gateway turn posts a same-turn `multica issue comment add <uuid> --content "..."` to the governing issue before replying — Multica is the memory, never local-only. Log the decision/result, not the process.
- Treat a bare `Update` / `Update progres` as a complete status request — pull `hermes cron list` plus the in_progress count immediately and report; never ask what to update.
- A reel draft delivered to Telegram is not a post — claim Instagram published only after a permalink read-back via GET_IG_MEDIA (or newest-item lookup confirming the media id), since PRIVATE accounts stay ACTIVE in Composio yet reject publish.
- When `issue create` output truncates before showing the UUID, recover with `issue search "<exact title>" --include-closed` and parse identifier/id from the result — never create a second issue for the same request.
- Parse `issue list` from the `issues` array (`total`, `has_more`, `limit`, `offset` envelope) — the payload has no `items` key, so reading `items` silently yields zero and undercounts the board.
- Dedup in two passes before any cancel-many: first exact duplicates via normalized titles (lowercase, strip dates/times/counts, collapse whitespace, require length) so dated recurrences group; then thematic clusters via regex over title plus description head (trend scans, reels, bake-offs, power calcs, forex, status updates, OCR, backups) — title-only grouping misses same-work different-name issues. Present each cluster with identifier, status, and title in a scope-inventory prompt and execute only approved IDs, commenting the reason on each issue before flipping status.
