# hermes-bot-company

**Pack:** bot-company-os

## Use case

Use when running Darren's Hermes multi-bot company.

## How to use it

## Procedure

1. **Inventory the fleet first** — run `hermes profile list` and `hermes gateway list` to see which bots exist and which gateways run, since profiles already on disk are departments waiting to be woken, not creations.
2. **Wake and verify with a one-shot ping** — run `hermes -p <bot> chat -q "reply with exactly: <bot> READY"` per bot and treat a coherent reply as alive; read the full output (`tail`, never grep for the header border — the answer body sits between the header box and the Resume lines, so border-grep hides it). For 3+ bots, fan wake/report pings out via parallel `delegate_task` children (2–4 bots each) instead of a serial shell loop — one free-tier ping takes ~15–35s, so a serial loop over the whole fleet costs many minutes while a fan-out finishes in about one ping. Keep output tails generous (`tail -n 20` or more): a short tail can slice off the answer box and force a wasted re-ping. Expect free-tier rate-limit fallbacks and report the fallback model honestly.
3. **Delegate isolated work with verifiable proof** — give each worker its full context plus the required evidence (file path, URL, test output), because child summaries are self-reports that must be re-read before passing upward.
4. **Review then re-delegate to improve** — check the proof yourself, then hand the same artifact to a second bot for critique or rework; the manager never forwards an unchecked result to the CEO. Re-verify workers' outside-world claims (live headers, endpoint status, upstream behavior) with your own probes before shipping — a confident wrong note committed to code ships as your bug.
5. **Split long builds into committable steps** — research, write file, commit, repeat — so a model-side mid-run failure keeps partial progress; retry the failed step, never the whole job.
6. **Keep durable work on durable systems** — use kanban plus cron routines for anything that must survive restarts, since background delegation children die with the parent process.
7. **Track the org in Multica** — one project for the company, one issue per build-out, status moved with `--no-start` and verified by read-back the same turn.
8. **Assign one brain per bot from the free roster** — read the live Nous free list from `nous_recommended_cache.json:freeRecommendedModels` (Solar-Pro-4, Longcat, Ling-Flash-Sante/Fin, Laguna-S/XS, Step-Flash) and pin each worker with `hermes config set` on its profile; verify with `hermes fallback list` before benchmarking role-fit, since the cached roster drifts without notice. Split the fleet Nous-free vs local-OmniRoute by role: reasoning-light assistants on Nous free (`provider nous`, Zen base URL), heavy specialists on the local proxy (`provider custom`, `model.base_url http://127.0.0.1:20128/v1`, `model.api_key not-needed`, `model.default auto/best-*`) with a Nous Solar-Pro-4 fallback each, since local `auto/*` routes answer at zero cost.

## Pitfalls

- Never publish, spend, change external accounts, or run destructive actions without Darren's explicit tap — gate those behind CEO approval every time.
- Verify publishing gates before promising a post — Instagram PRIVATE accounts read fine but reject Graph publish (needs Business/Creator plus a linked Facebook Page); YouTube multipart upload needs a staged file reference, not a local path; credit-gated generation needs a balance check first with a credit-free fallback ready.
- Pass the explicit account alias on every multi-account toolkit read — the unscoped call fails and returns the alias list, so resolve the alias before the real read.
- Ship every brand drop as a complete pack — thumbnail, titled video, rewritten captions, live links filled in — never announce assets without delivering them natively in-chat.
- Keep one-process-per-profile for isolation; switch the default gateway to multiplexing only when process count becomes the operational burden.
- Pin each specialist bot to its own model and minimal skill set — a fleet sharing one model and full tools loses the cost and focus advantage.
- Set primary with `hermes config set model.provider <provider>` plus `hermes config set model.default <model>` and `hermes config unset model.base_url` to drop stale overrides; set fallback with `hermes config set fallback_providers '[{"provider": "<p>", "model": "<m>"}]'` — Solar-Pro-4 free is `nous/upstage/solar-pro4:free`, Muse-Spark free is `opencode-free/muse-spark-1.3-contributor-free` (keyless).
- Reach the local proxy via `http://127.0.0.1:20128/v1`, never `localhost` — the proxy listens on IPv4 while `localhost` can resolve to `::1`, so the same request succeeds on the IP and fails on the name.
- Vet binary sources before pulling anything — a mirror advertising unlocked/pro/mod copies is a
  cracked distribution, so refuse the download and offer store-metadata analysis plus a clean-room
  rebuild instead, since cracked pulls carry piracy liability plus malware and store-ban risk.
- Block non-consensual person-identification at spec time — face-ID on strangers ships as stalking
  tooling and draws store bans, so constrain lookup apps to on-device triage plus user-opened public
  link-outs behind a visible consent rule, never a face database.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/hermes-bot-company/`). Video walkthrough ships with the pack.
