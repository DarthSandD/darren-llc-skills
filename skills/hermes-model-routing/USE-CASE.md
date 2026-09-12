# hermes-model-routing

**Pack:** bot-company-os

## Use case

Use when switching Hermes primary/fallback models.

## How to use it

## Procedure

1. **Resolve exact model IDs before writing anything.** Query the provider's live `/v1/models` and filter for the family (e.g. `muse`, `solar`, `spark`); confirm free-tier suffixes (`-contributor-free`, `:free`) against `cache/endpoint_model_metadata.json` and `cache/nous_recommended_cache.json`. Never guess a suffixed ID — `muse-spark-1.3` and `muse-spark-1.3-contributor-free` are different routes.
2. **Set the primary with config commands:** `hermes config set model.provider <provider>` then `hermes config set model.default <model-id>`. Then `hermes config unset model.base_url` to drop any stale override so the provider's own base URL applies.
3. **Set the fallback chain non-interactively.** `hermes fallback add` is an interactive picker and cannot be driven from automation — instead write the chain directly: `hermes config set fallback_providers "[{\"provider\": \"<p>\", \"model\": \"<m>\"}]"`.
4. **Verify by reading back.** Run `hermes fallback list` (shows Primary + chain in one view) and `hermes config get model` / `hermes config get fallback_providers`. Never claim the switch succeeded without this read-back.
5. **Offload cron jobs to a cheap fast model when the primary is rate-limited.** `hermes config set cron.model <model-id>` + `hermes config set cron.model_provider <provider>` pins every agent cron job at once; `hermes cron doctor` showing HTTP 429 on jobs is the diagnostic signal that crons are burning primary quota.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/hermes-model-routing/`). Video walkthrough ships with the pack.
