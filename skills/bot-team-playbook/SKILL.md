---
name: bot-team-playbook
description: "Use when running bot teams with orchestrator QC."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [hermes, bots, multi-agent, delegation, company]
    category: autonomous-ai-agents
---

# Bot Team Playbook (Hermes Bot Mode, via Callum/Waterloots video)

Source: https://youtu.be/3RoK0rrOHCA — orchestrator + researcher + librarian pattern with human-in-loop approval.

## Rules

1. One bot, one job: profile owns its chat, role, model, memory, skills, avatar. No universal agents.
2. SOUL states team fit: every worker's default handoff is the orchestrator (qc-reviewer), never sideways.
3. Smartest model on top, free/local models below: manager/orchestrator on muse-spark or Solar-Pro-4, bulk workers on free tier.
4. Trim before running: skills lazy-load but tools are always live, so prune tools first. Unneeded tools cause extra calls, quota burn, and 10-20 min loops (e.g. worker self-delegation).
5. Researcher trim reference: OFF delegation, code execution, session search, cron. ON files, terminal, web search, browser automation, grounded citations.
6. Orchestrator trim reference: OFF web, browser, computer-use, sub-delegation. ON files, session search, read-only verification.
7. Groups are 2-6 bots with 3-round / 10-message caps: @name scopes speakers, silence means pass, @user escalates to Darren. On cap-hit mid-fix, say "@worker please continue" or DM directly.
8. Bound every task: source caps, word caps, fixed file path, idle timeout >= 10 min for long runs.
9. Cite or cut: numbers and research carry sources or file links, else the claim is removed.
10. Refine after runs: ask each bot what it learned (paths, retry rules) and persist to memory so the next run skips a step.
11. Solo-vs-team judgment: a top model alone can beat a team on quality; teams win on cost and repeatability. Route repeatable work to teams, one-offs to the smartest solo.
12. Manager never forwards unchecked results: re-read file proof, apply the QC gate, allow one focused revision, then report.
13. Orchestrator never does the work itself: its only job is break down and hand out (one worker per card, parallel). The moment it starts executing, everything bottlenecks — manager manages, workers work. One command moves the board.
