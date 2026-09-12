# Skill 03 FULL — cloudflare-deploy

## S01 COLD OPEN
Deploy day. You run the command, the shell hangs on a login prompt, the page returns five-twenty-two, and the project name you typo'd is permanent. This skill makes Cloudflare boring. Twelve briefs, eight minutes, zero deploy drama after.

## S02 PIN IT DOWN
Brief one: pin wrangler and stay non-interactive. CI equals one plus npx yes wrangler at a pinned version. Pinned version kills surprise reinstall stalls. CI equals one kills auth and progress prompts that hang your shell mid-session. Confirm identity once with wrangler whoami, then never think about it again.

## S03 PROJECT FIRST
Brief two: create the Pages project before the first deploy. A first pages deploy to a nonexistent project fails — always. Run pages project create with your name and production branch main first. The free address is your-name dot pages dot dev. Sixty seconds now saves a failed deploy later.

## S04 PORTABLE BUILD
Brief three: build portable, bake the API origin. Vite base dot-slash serves under any domain or subpath with zero rebuilds. Bake the worker origin at build time through an env define, then grep the bundle to confirm the origin actually landed. If grep finds nothing, you shipped a frontend pointing at nobody.

## S05 LONG DEPLOYS
Brief four: long uploads go to background. Pages deploy with hundreds of files takes minutes — background it with notify, never block your session. Worker deploys are wrangler deploy with the file, the name, and the compatibility date. No date flag, no deploy — config-less deploys get rejected without it. Always pass it explicitly.

## S06 NAMES AND DOMAINS
Brief five: there is no rename. Hate the free address? Create a new project and deploy the same dist folder — no rebuild when the base is portable. Delete the old project only after the owner confirms. Custom domains need ownership first: check membership read-only before promising an attach. An unregistered domain must be bought before anything points at it.

## S07 VERIFY LIKE A VISITOR
Brief six: verify like a visitor, not like the deploy log. HTTP two hundred on the apex. The rendered page title. The main hashed JavaScript bundle. A five-twenty-two right after the first deploy is propagation — wait thirty seconds and recheck before rebuilding anything. Rebuilding on propagation is how you queue three deploys to fix zero bugs.

## S08 SWEEP THE ROUTES
Brief seven: sweep the worker routes live. Curl every slash-api path and read real status codes — not what the code should return, what the edge actually returns. Log results to the tracker the same turn. A route you never curled is a route you never shipped.

## S09 DUAL HOME
Brief eight: dual-home releases go out identically. Same portable dist to pages dot dev and github dot io, every release, then assert both serve the same hashed bundle. Resolve the worker origin at runtime — same-origin first, absolute worker URL where there is no slash-api. One build serves both homes. Divergent homes mean bug reports against a stale copy you already fixed.

## S10 LIVE VIDEO
Brief nine: live video goes through the worker proxy, never direct. Upstream camera streams have no desktop playback and no CORS headers. The recipe is playlist-rewrite plus segment-proxy, and it lives in the skill's reference file. Proxy it or the player stays black.

## S11 PITFALLS
Brief ten: the pitfalls. Never passthrough-proxy an upstream with fixed path prefixes — map the real paths or the worker faithfully forwards a 404. Keep the response contract identical to what the client validates. Treat upstream 429s as honest pass-through, not worker bugs. Run node check on the worker file before deploying — a syntax error ships a dead API. After secret put, wait thirty seconds — the running deployment picks up secrets on a delay.

## S12 FREE TIER MATH
Brief eleven: the money. Pages free tier, Workers free tier — this whole pipeline costs zero for side projects. You pay when you scale, not to learn. That is why this skill sits this early in the library: free hosting removes every excuse between you and shipped.

## S13 CLOSE
Recap: pin it, project first, portable build, background longs, no rename, verify like a visitor, sweep routes, dual-home identical, proxy the video, respect the pitfalls. Free on GitHub, link below. Next ascending: community-launch — where your deploys get an audience. Comment SHIPPED with your pages dot dev link. Go.

## S14 PREVIEWS
Brief twelve: every branch gets a preview. Push a branch, Pages builds a preview URL automatically. Review the real thing, not screenshots — click it, break it, comment on it. Merge only what survived the preview. This one habit catches more bugs than any checklist in this library.

## S15 ROLLBACK
Brief thirteen: bad deploy? Roll back, don't hotfix forward at midnight. Pages keeps every deployment — find the last good one, promote it, breathe. Then fix the bug on a branch like a professional. Rollback is a button, not a confession.

## S16 ANALYTICS
Brief fourteen: turn on Web Analytics. Free, private, no cookie banner. You'll see which pages get visited and which get ignored — that data picks your next build better than any roadmap meeting. Ship, measure, then decide.

## S17 FLAGS VS FILES
Brief fifteen: flags beat files for agents. A wrangler dot toml is great for humans who read repos. An agent passing name, file, and date as flags is explicit every run — no hidden config, no stale file, nothing to misread. Explicit over implicit, every deploy.

## S18 WHEN NOT
Brief sixteen: when not Cloudflare. Need a real server process, websockets beyond limits, or heavy background jobs? Take the VPS. Need Docker images and private networking? Take the container host. Cloudflare is the edge for sites and light APIs — pick the tool that fits instead of forcing everything through one door.

## S19 SECRETS
Brief seventeen: secrets go in, never in code. Dashboard or secret put, then wait thirty seconds and re-read the live endpoint — propagation delay is real and it has fooled everyone once. Never commit a token. If one leaks, rotate it before you finish reading this sentence.

## S20 CLOSE TWO
Recap two: previews, rollback, analytics, flags, right tool, secret hygiene. Full recap in the description. Skill files free on GitHub. Next: community-launch. Comment SHIPPED with your link. Go.

## S21 FAQ
Final brief: questions everyone asks. Is the free tier really free? Yes — Pages and Workers free tiers cover side projects with room to spare. Do I need a credit card? No. Wrangler or dashboard? Wrangler — scripted beats clicked, and scripts live in git. What breaks most? Secrets committed to repos and renames attempted on projects — both covered above. Full recap plus timestamps in the description. Skill free on GitHub. Next: community-launch. Comment SHIPPED. Go.
