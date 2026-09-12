---
name: linkedin-posting
description: 'Use when posting to LinkedIn via Composio.'
---

# LinkedIn Posting (via Composio)

Publish, verify, and delete LinkedIn feed posts through the Composio `linkedin` toolkit (ACTIVE connection). Feed posts are API-supported; profile chrome is not.

## Procedure

1. Open a session with `COMPOSIO_SEARCH_TOOLS` for the exact use case and reuse its `session_id` on every follow-up call.
2. Fetch the author URN first: `LINKEDIN_GET_MY_INFO` → `urn:li:person:<id>`.
3. For an image post, register then upload: `LINKEDIN_REGISTER_IMAGE_UPLOAD` (`owner_urn` + `feedshare-image` recipe) returns `upload_url`; PUT the bytes with native curl including an explicit `Content-Type` (LinkedIn answers 400 without it) plus the returned media-type header.
4. Publish with `LINKEDIN_CREATE_LINKED_IN_POST` (`author`, `commentary`, `visibility: PUBLIC`, `lifecycleState: PUBLISHED`, optional `LEARN_MORE` CTA + landing page). Its `images[]` field only accepts Composio S3 keys — a raw uploaded asset URN cannot be attached there, so default to text + link and let the URL unfurl render the preview.
5. Verify with `LINKEDIN_GET_POST_CONTENT`, and report a 403 there honestly as an API scope gap (ask the owner to eyeball the live post) — never as proof of failure. `LINKEDIN_DELETE_POST` accepts the `share` URN and treats a repeat 404 as success, so a clean delete confirms the post had really existed.

## Job search (Dice, not LinkedIn)

- The `linkedin` toolkit exposes profile and feed posts only — it has no job-search or apply endpoint, so run discovery through `DICE_MCP_SEARCH_JOBS` and detail through `DICE_MCP_GET_JOB_DETAILS`, or via Composio web search (`COMPOSIO_SEARCH_WEB` for leads → `COMPOSIO_SEARCH_FETCH_URL_CONTENT` to read each posting).
- Pass the search result's `guid` (never the internal `id`) as `job_id` — the detail endpoint rejects `id` values.
- Present each pick with both `detailsPageUrl` and `companyPageUrl` plus the AI-search disclosure, and draft only until the owner gives an explicit GO — never auto-apply.

## Lead verification (every batch)

- Read every shortlisted posting; rank by fit, salary band, and entry requirements — never rank on titles alone.
- Drop SEO-mirror/aggregator domains with no real company site behind them (resume farms) and location-gated roles the owner can't satisfy; say briefly why each was cut.
- A masked contact in the text is not an address — route to the posting's form/apply link.
- Ship a paste-ready kit as a file: per-role why-you-fit, honest screening answers (years, location, work auth, degree status, salary line), and order of attack. Owner pastes and clicks; bot-applying gets the account flagged.

## Gates (always on)

- Never publish without an explicit GO — a draft first, then the post.
- Feed posts: YES via API. Background banner and Featured slots: NO API exists — the owner taps those in by hand (a fresh feed post can be pinned to Featured in seconds).
- Keep fork/MIT credit in launch copy — one line protects the owner's reputation if anyone checks provenance.
- A manual Featured/banner save that fails with LinkedIn's own 'Something went wrong on our end' is their backend refusing, not the owner's device — retry after ~1h, save the item WITHOUT the link first then edit the link in, or swap the URL (their preview crawler chokes on JS-heavy sites; a plain repo link unfurls fine).
