# github-pages-deploy

**Pack:** ship-deploy

## Use case

'Use when deploying a static site to GitHub Pages.'

## How to use it

## Procedure

0. Confirm how Pages is actually sourced before touching anything: `gh api repos/<owner>/<repo>/pages --jq '{build_type, source}'`. A legacy `gh-pages` branch source means you deploy by pushing that branch; an Actions `build_type` means merging to main is the deploy and pushing `gh-pages` does nothing.
0b. When the user reports a pushed fix 'still not working' on the live site, fetch the live URL and grep for the fix marker BEFORE debugging code — a stale deploy or an undeployed copy is the common cause, and no code change fixes that.
1. Build with the subpath baked in (Vite: `--base=/<repo>/`, or `base: './'` for builds that must work under any subpath or custom domain). A root-built bundle 404s every asset under a project subpath. The base only rewrites bundler-emitted URLs — grep the source for hardcoded root-absolute refs (`src="/..."`, `href="/..."`, and string literals such as `"<img src=\"/x.svg\">"` inside JS) and make them relative, since the bundler leaves those untouched. After building, assert zero `src="/` / `href="/` remains in `dist/index.html`.
2. Confirm the fork remote (`git remote -v`) and push the branch there — never to upstream.
3. Fresh-clone the fork, checkout an orphan `gh-pages` branch, wipe tracked files, copy `dist/` contents FLAT into the root (no nesting level), add `.nojekyll`, commit, push.
4. Enable Pages: create the Pages site if missing; if already enabled, set source branch to `gh-pages`, path `/`. Poll the site until `status` leaves `building`.
5. Verify like a visitor, not like the builder: HTTP 200 on `/`, on the main JS bundle, and on the largest engine/vendor file (e.g. a 3D or map runtime). A 200 index with a 404 engine renders a blank page with a correct title — the title check alone proves nothing. Confirm the rendered `<title>` matches the intended brand.

## Pitfalls

- Nested-root deploys (all files one folder too deep) serve the index but 404 every asset — diff the pushed tree against `dist/` when the page loads blank.
- Rebuilds that run concurrently with a copy or upload step produce half-old trees; sequence build, then copy, then push, never overlapped.
- Rebrands applied to build output get wiped by the next build — patch the SOURCE title/meta, rebuild, and re-verify.
- Dev-server proxies and middleware (`/api/...` routes) do not exist on Pages and 404 there — grep for `/api/` fetches before promising a server-backed feature works statically. When no backend exists, replace with keyless client-side equivalents; when a Worker backend exists, keep the feature via runtime worker-base fallback — same-origin first, absolute worker URL on backend-less hosts (hostname check plus `?api=`/`window` override plus one retry on 404/405/501) — because the static host will never grow the route. Apply the base to media-element src builders (`<img>`/`<video>`/HLS playlist URLs) as well as `fetch()`, since element srcs never retry and fail silently on the wrong origin.
- Judge third-party direct-fetch viability with an Origin-bearing request — `curl -H "Origin: https://<user>.github.io"` — and read the returned `Access-Control-Allow-Origin`, never the no-Origin response (servers commonly omit the header without one, which falsely reads as blocked). Distrust code comments claiming open or blocked CORS; live headers are the verdict.
- After pushing, poll the live index until it references the NEW hashed asset names before claiming a fix is live — Pages serves the stale build for a minute or two, and a premature check validates the old bundle.
- Pushing from a detached-head worktree with `git push origin gh-pages` silently reports up-to-date while pushing the stale branch ref — push `HEAD:gh-pages` so the checked-out commit is what actually uploads.
- When local and remote branch names differ (e.g. local `master`, remote `main`), prove ancestry with `git merge-base --is-ancestor origin/main <local>` and diff the trees before any cross-branch push — a blind `push <local>:main` can clobber the live branch.
- Pushes failing with 'Author identity unknown' mean the repo has no identity — set a repo-local `user.name`/`user.email`, never a global one.
- When `dist/` is gitignored, `git subtree push --prefix dist` fails (`'dist' does not exist`) — deploy through a dedicated worktree instead: check out the branch, clear everything except `.git`, copy `dist/` flat, commit visibly, push. If the push rejects on a moved remote, reset the worktree to the origin tip and re-lay the tree — snapshot deploy branches never rebase-merge, the fresh build always wins.
- Never silence git's output on deploy steps (`-q`, `2>/dev/null`, `| tail`) — a quiet `git add` can fail while `git rm --cached` succeeded, committing deletions only and shipping an empty branch (apex 404). Stage visibly, then assert `git ls-files | wc -l` is nonzero and `git ls-tree HEAD --name-only` lists the tree before pushing.
- Dual-surface standing spec: every release ships the identical `dist/` to Pages AND `gh-pages` — verify the new hashed bundle on both live URLs before reporting. Phone users keep the old build via cached index.html, so pair the report with fresh-load steps (kill the tab / private tab) or a shipped fix reads as still broken. The repo README rides every release too — update its deployment/feature sections with each fix and push it alongside the code.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/github-pages-deploy/`). Video walkthrough ships with the pack.
