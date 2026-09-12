---
name: hermes-model-routing
description: "Use when switching Hermes primary/fallback models."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [hermes, models, providers, fallback, configuration]
---

# Hermes Model Routing

Configure the Hermes primary model and fallback chain non-interactively, with verified model IDs.

## Procedure

1. **Resolve exact model IDs before writing anything.** Query the provider's live `/v1/models` and filter for the family (e.g. `muse`, `solar`, `spark`); confirm free-tier suffixes (`-contributor-free`, `:free`) against `cache/endpoint_model_metadata.json` and `cache/nous_recommended_cache.json`. Never guess a suffixed ID — `muse-spark-1.3` and `muse-spark-1.3-contributor-free` are different routes.
2. **Set the primary with config commands:** `hermes config set model.provider <provider>` then `hermes config set model.default <model-id>`. Then `hermes config unset model.base_url` to drop any stale override so the provider's own base URL applies.
3. **Set the fallback chain non-interactively.** `hermes fallback add` is an interactive picker and cannot be driven from automation — instead write the chain directly: `hermes config set fallback_providers "[{\"provider\": \"<p>\", \"model\": \"<m>\"}]"`.
4. **Verify by reading back.** Run `hermes fallback list` (shows Primary + chain in one view) and `hermes config get model` / `hermes config get fallback_providers`. Never claim the switch succeeded without this read-back.
5. **Offload cron jobs to a cheap fast model when the primary is rate-limited.** `hermes config set cron.model <model-id>` + `hermes config set cron.model_provider <provider>` pins every agent cron job at once; `hermes cron doctor` showing HTTP 429 on jobs is the diagnostic signal that crons are burning primary quota.

## Rules

- Use `hermes config set/unset` for every change — never hand-edit `config.yaml` (a stray indent breaks the live gateway).
- Keep provider names exact: `opencode-free` (keyless free tier) is not `opencode-zen` / `nous`, even though they share a base URL.
- Solar-Pro-4 free does not appear in the Zen `/v1/models` list — its route is provider `nous`, model `upstage/solar-pro4:free`.
- One fallback entry is a complete chain; clearing the old `moa/default` entry happens automatically when the new chain is written.
- `cron.model` / `cron.model_provider` ship empty, so crons silently inherit `model.default` and burn primary quota — set them explicitly on any box with scheduled jobs.
- Prefer a cron provider different from the primary provider (e.g. crons on `nous` while interactive runs on `opencode-free`) — a different model on the same provider still shares the rate-limit bucket.
- Darren's standing primary is muse-spark-1.3 on the free tier — after any work done on a fallback or reasoning model, switch the primary back and verify with `hermes fallback list`; never leave the box parked on fallback.
- Per-job `--model` pins override `cron.model`, and `--no-agent` script jobs skip the LLM entirely — check both before assuming every job follows the cron default.
