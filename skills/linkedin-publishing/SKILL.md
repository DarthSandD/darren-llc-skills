---
name: linkedin-publishing
description: 'Use when publishing LinkedIn posts via Composio.'
---

# LinkedIn Publishing via Composio

Feed posts are API-possible. Profile edits (background banner, Featured slots, headline) are NOT — LinkedIn exposes no API for them; the user does those by hand in the app. Never claim a profile edit; offer the feed-post-then-pin workaround instead (publish post, user taps Pin to Featured).

## Procedure

1. Identity first: `LINKEDIN_GET_MY_INFO` returns the person id. Build the author URN as `urn:li:person:<id>` — never a placeholder. The post author and the image owner must be the same URN.
2. Image slot: `LINKEDIN_REGISTER_IMAGE_UPLOAD` with `owner_urn` and recipe `urn:li:digitalmediaRecipe:feedshare-image` returns `upload_url` plus `asset_urn`. Company recipes with a person owner return 403.
3. Byte upload: PUT the file to `upload_url` with BOTH headers `Content-Type: <real mime>` and `media-type-family: STILLIMAGE`. Omitting either returns 400; success is 201. Prefer native curl for the PUT over hand-rolled HTTP clients.
4. Publish: `LINKEDIN_CREATE_LINKED_IN_POST` with `author`, `commentary` (max 3000 chars, plain text, link inline for unfurl), `visibility: PUBLIC`, `lifecycleState: PUBLISHED`. Add `contentLandingPage` plus `contentCallToActionLabel: LEARN_MORE` when the post drives to a URL.
5. Confirm from the create response (`urn:li:share:...`). Roll back a wrong public post with `LINKEDIN_DELETE_POST` (accepts share or ugcPost URNs; a repeat 404 counts as deleted).

## Pitfalls

- The create call's `images[]` field only accepts Composio-internal S3 keys, not LinkedIn asset URNs — a natively uploaded banner cannot be attached through it, so ship text-plus-link posts and let the URL preview carry the visual.
- `LINKEDIN_GET_POST_CONTENT` can return 403 on your own just-published post (scope gap) — a create-success response is the confirmation, and a subsequent successful delete proves the post existed; report read-back blocks honestly instead of claiming the post is publicly visible.
- Ask before every publish and every delete: both are external, reputation-touching actions, even when a prior message seemed to approve the general direction.
- A manual Featured save failing with 'Something went wrong on our end' on both app and desktop is LinkedIn's backend refusing, not the account — run the retry ladder (wait ~1h and refresh, save the slot without the link then edit the link in, swap to a plainer URL since the preview crawler chokes on JS-heavy sites) and ship unrelated work meanwhile instead of blocking on it.
- Land the usage record plus brand kit in the repo before drafting launch copy — a USAGE file (capture/read/rasterize flow with screenshot proof and run steps) and a BRAND file (one-liners, links block, hashtags, visual tokens) pushed to main and verified live, so every claim in the post links to an artifact instead of a promise.
