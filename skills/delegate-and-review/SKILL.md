---
name: delegate-and-review
description: "Delegate builds to workers, review before handover."
version: 2.0.0
author: Hermes Agent
---

# Delegate-and-Review Pattern

## When to use
Any Darren request with substantial coding, building, or multi-step execution. Pattern: delegate heavy lifting to worker, review yourself, hand finished artifact to Darren.

## How to delegate
Single: `delegate_task(tasks=[{"context": "<constraints + domain>", "goal": "<specific done-state>"}])`. Batch: all tasks in ONE call with tasks array. Default max_concurrent_children is 10 here.

Worker brief template (always use this):
```
Role: You are a strong generalist coding agent.
Goal: <specific done-state>
Constraints: free/keyless first, no placeholders, runnable as-is, keep it tight.
Domain: <app|script|automation|research|dashboard>
Deliver: absolute file path(s) + what you ran to verify.
Do NOT: guess APIs, leave TODOs, add fluff.
```

## Domain playbooks
- App: single-entry runnable, list deps, include quick run steps.
- Script: stdlib first, argparse for inputs, prints clean output + saves file.
- Automation: idempotent, logs actions, dry-run flag where risky.
- Research: cited sources, dates, no invented facts.

## Review gates (all must pass)
G1 Done-state match — does output equal the goal?
G2 Runs live — execute it yourself, paste real output.
G3 No stubs — grep TODO/placeholder/lorem, reject if found.
G4 Darren prefs — free/keyless, working attachment, no fluff.
G5 Multica logged — comment same turn to governing issue.
If any gate fails: steer once with exact fix, else re-delegate.

## Review checklist (do yourself, never skip)
- Read delivered file(s)
- Does it do what was asked? Run it if feasible
- No hallucinated APIs, no placeholder stubs
- Darren prefs: free/keyless first, working artifact, no fluff

## Delivery
Working artifact as native Telegram attachment (MEDIA: path). Never describe what you would do. If review fails, steer worker or re-delegate.

## Steer
`delegate_task(action="steer", subagent_id="sa-...", message="<correction>")`

## When NOT to delegate
One-shot lookups, simple edits — do yourself. Tasks needing user interaction — children cannot ask. Durable work — use cronjob.
