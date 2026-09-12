---
name: cron-jobs
description: 'Use when managing Hermes cron jobs.'
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows]
metadata:
  hermes:
    tags: [cron, scheduler, automation, telegram]
    category: productivity
---

# Cron Jobs

Manage Hermes scheduled jobs (`cronjob_manage`): recurring briefings, digests, pipelines, health checks, backups. Every change must survive the next unattended fire — there is no human in the loop to catch a regression.

## Procedure

1. **Inspect first** — `list` returns truncated prompt previews only. Full prompts live in `~/AppData/Local/hermes/cron/jobs.json`; scripts in `~/AppData/Local/hermes/scripts/`.
2. **Update prompts by appending** — `update` with `prompt` replaces the whole prompt, so reconstruct the exact current text from `jobs.json`, append the new section, and send the full text. Never rewrite from a preview.
3. **Style deliveries via `templates/message-style.md`** — append the block to agent-job prompts; it governs HOW the final message reads while the spec governs WHAT it says.
4. **Treat no_agent stdout as the delivered message** — every printed line reaches Telegram, so keep debug lines on stderr or delete them; quiet ticks print one short line. When the user asks for always-alert, forbid `[SILENT]` in the prompt explicitly AND make the script print the full panel on every tick — empty stdout delivers nothing, which reads as a broken job.
5. **Forward whole blocks through wrappers** — a filter that forwards only matching lines silently drops detail lines; slice from the headline to end of stdout instead.
6. **Test directly** — run each touched script (`python3 <script>`, check exit code + stdout) and read back each updated job before relying on the schedule.
7. **Track in Multica** — one issue per cron workstream in Darren111, closed on verified delivery.

## Pitfalls

- `list` shows `job_id`; the record key inside `jobs.json` is `id` — query the file with the `id` key or every lookup misses.
- A suppressed/cooldown path that prints nothing reads as a dead job — emit a compact `⚠️ [suppressed …]` line so silence means healthy, not broken.
- Label an unreadable sensor `n/a` with the fix (e.g. needs LibreHardwareMonitor) — never invent a reading to fill the row; a fabricated temperature destroys trust in every real number around it.
- Firing a job with `run` delivers to its real channel — never use it as a formatting test; test scripts locally instead.
- Set a wrapper's subprocess timeout above the slowest inner probe — deep checks on a loaded disk blow past 60s, and a `TimeoutExpired` traceback into a no_agent channel delivers as a scary 'cron failed' notice for a job that was merely slow; on timeout, append a note to the log and exit 0 so the next tick retries silently.
- Throttle repeat alerts by cooldown but always fire a NEW failure set immediately — compare the sorted failing-check signature against the last alerted one, or a genuinely new outage stays silent behind an old cooldown.
- Quote both the schedule and the prompt as single args to `hermes cron create` (`"0 10 * * *" "Do X. Report back."`) — a bare multi-word prompt is parsed as stray flags and dies with `unrecognized arguments`.
