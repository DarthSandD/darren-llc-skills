# cloudflare-deploy

**Pack:** ship-deploy

## Use case

'Use when deploying to Cloudflare Pages or Workers.'

## How to use it

## Procedure

1. **Pin wrangler and stay non-interactive** — `CI=1 npx --yes wrangler@<version> ...` (pinned version avoids surprise reinstall stalls; `CI=1` stops auth/progress prompts from hanging the shell). Confirm identity once with `wrangler whoami`.
2. **Create the Pages project before the first deploy** — a first `pages deploy` to a nonexistent project fails; run `pages project create <name> --production-branch main` first. The free address is `https://<name>.pages.dev/`.
3. **Build portable, bake the API origin** — Vite `base: './'` serves under any domain or subpath with no rebuild; bake the worker origin at build time through an env define (e.g. `GEV_API_BASE=https://<api>.<account>.workers.dev npm run build`) and grep the bundle to confirm the origin landed.
4. **Deploy long uploads in background** — `pages deploy dist` with hundreds of files takes minutes; run background with notify, never block the session. Worker deploys are `wrangler deploy workers/<file>.mjs --name <api-name> --compatibility-date <YYYY-MM-DD>` — config-less deploys are rejected without the date flag, so always pass it explicitly.
5. **Rename the free address by creating a new project** — there is no rename; `pages project create <new-name>` + deploy the same `dist/` (no rebuild when the base is portable). Delete the old project only after the owner confirms.
6. **Custom domains need ownership first** — wrangler v4 has no zone listing; check membership read-only via the Cloudflare API with the OAuth token from the wrangler config before promising an attach. An unregistered domain must be bought (registrar dashboard, owner's purchase) before anything can point at it.
7. **Verify like a visitor** — HTTP 200 on the apex, the rendered `<title>`, and the main hashed JS bundle. A 522 immediately after the first deploy is propagation — wait ~30s and recheck before rebuilding anything.
8. **Sweep the worker routes live** — curl each `/api/*` path and read real status codes; log results to the tracker the same turn.
9. **Ship dual-home releases identically** — when the site lives on two addresses (e.g. `pages.dev` + `github.io`), deploy the same portable `dist/` to both every release and assert both serve the same hashed bundle. Resolve the worker origin at runtime (same-origin first, absolute worker URL on hosts without `/api`) rather than per-home build-time defines — one build then serves both homes, and divergent homes mean bug reports against a stale copy you already fixed.
10. **Serve live HLS through the worker proxy, never direct** — upstream HLS (toll/city CCTV) has no native desktop playback and no CORS; the playlist-rewrite + segment-proxy recipe lives in `references/hls-worker-proxy.md`.

## Pitfalls

- Never passthrough-proxy an upstream that requires fixed path prefixes — map to the real upstream paths in the worker (e.g. Radio Browser needs `/json/...`), or the worker faithfully forwards a 404.
- Keep the worker's response contract identical to what the client validates — port the client's normalizer/validator subset (id shape, https-only URLs, codec/geo gates) into the zero-dep worker file so every emitted row passes.
- Treat upstream 429/503 as honest pass-through, not worker bugs — throttle and unconfirmed-source states are the upstream talking; only fix what the worker itself misshapes.
- Keep stateless-worker endpoints dependency-free in one file — no served-sets or cross-request memory; accept any well-formed id for accounting endpoints and return 204.
- Run `node --check` on the worker file before deploying — a syntax error ships a dead API.
- After `secret put`, wait ~30s and re-read the live endpoint before concluding — the running deployment picks up the new secret value on a delay, so an immediate check still reflects the old catalog.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/cloudflare-deploy/`). Video walkthrough ships with the pack.
