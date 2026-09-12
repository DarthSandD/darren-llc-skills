# linkedin-posting

**Pack:** growth-money

## Use case

'Use when posting to LinkedIn via Composio.'

## How to use it

## Procedure

1. Open a session with `COMPOSIO_SEARCH_TOOLS` for the exact use case and reuse its `session_id` on every follow-up call.
2. Fetch the author URN first: `LINKEDIN_GET_MY_INFO` → `urn:li:person:<id>`.
3. For an image post, register then upload: `LINKEDIN_REGISTER_IMAGE_UPLOAD` (`owner_urn` + `feedshare-image` recipe) returns `upload_url`; PUT the bytes with native curl including an explicit `Content-Type` (LinkedIn answers 400 without it) plus the returned media-type header.
4. Publish with `LINKEDIN_CREATE_LINKED_IN_POST` (`author`, `commentary`, `visibility: PUBLIC`, `lifecycleState: PUBLISHED`, optional `LEARN_MORE` CTA + landing page). Its `images[]` field only accepts Composio S3 keys — a raw uploaded asset URN cannot be attached there, so default to text + link and let the URL unfurl render the preview.
5. Verify with `LINKEDIN_GET_POST_CONTENT`, and report a 403 there honestly as an API scope gap (ask the owner to eyeball the live post) — never as proof of failure. `LINKEDIN_DELETE_POST` accepts the `share` URN and treats a repeat 404 as success, so a clean delete confirms the post had really existed.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/linkedin-posting/`). Video walkthrough ships with the pack.
