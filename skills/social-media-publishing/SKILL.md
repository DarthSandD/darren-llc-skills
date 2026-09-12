---
name: social-media-publishing
description: 'Post to LinkedIn, Instagram, YouTube via Composio.'
---

# Social Media Publishing (via Composio)

Publish feed posts, Reels, and videos through Composio toolkits. Discover tools with `COMPOSIO_SEARCH_TOOLS` first, load schemas with `COMPOSIO_GET_TOOL_SCHEMAS`, execute with `COMPOSIO_MULTI_EXECUTE_TOOL` (pass the account alias at tool-item level, never inside arguments). Never publish without the user's explicit GO; confirm YouTube `privacyStatus` (public vs unlisted) before uploading.

## LinkedIn

1. `LINKEDIN_GET_MY_INFO` → author URN (`urn:li:person:<id>`).
2. Optional image: `LINKEDIN_REGISTER_IMAGE_UPLOAD`, then `PUT` the bytes with native curl including a `Content-Type` header (missing content-type 400s; success is 201).
3. `LINKEDIN_CREATE_LINKED_IN_POST` (commentary ≤3000 chars, `PUBLIC`, `PUBLISHED`, `LEARN_MORE` + landing page). A bare URL in commentary unfurls a link preview.
4. Verify with `LINKEDIN_GET_POST_CONTENT` when permitted; `LINKEDIN_DELETE_POST` confirms a post existed (a 404 means already gone).

## Instagram Reel

1. Stage the MP4 at a public clean-HTTPS URL with no query strings (a here.now direct file URL works).
2. `INSTAGRAM_POST_IG_USER_MEDIA` (`ig_user_id` numeric, `media_type: REELS`, `share_to_feed: true`) → container id.
3. `INSTAGRAM_POST_IG_USER_MEDIA_PUBLISH` with generous waits (`max_wait_seconds` 180, poll 5s) — video containers need 30–120 seconds of processing; containers are single-use, so a 400/409 means mint a fresh container and retry.
4. Verify with `INSTAGRAM_GET_IG_MEDIA` permalink read-back — claim published only after the permalink returns.

## YouTube

1. The file must be a Composio-staged `s3key`: in `COMPOSIO_REMOTE_WORKBENCH`, download the file (browser `User-Agent`, else 403) and call `upload_local_file` → `s3key`.
2. `YOUTUBE_MULTIPART_UPLOAD_VIDEO` (title, description with chapters + links, `categoryId` 28 for tech, tags, confirmed `privacyStatus`).
3. A 429 quota-exceeded on the shared connector project is a retry-tomorrow state, not a channel problem — keep the staged `s3key` + metadata and retry the next day.

## Pitfalls

- `LINKEDIN_CREATE_LINKED_IN_POST` `images[]` accepts Composio `s3key` references only — a registered LinkedIn asset URN cannot be attached through it, so post text+link when no staged file exists.
- LinkedIn exposes no API for background banners, Featured slots, or profile edits — feed posts only; pinning to Featured is always a manual user tap, so say so instead of implying automation.
- PRIVATE-typed Instagram accounts may still publish, but if Graph rejects with auth/permission errors the fix is Business/Creator conversion, not retries.
- For Reels, `video_url` values with query parameters are rejected (OAuthException 2207076) — use the clean direct file URL.
