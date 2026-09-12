# social-media-publishing

**Pack:** content-fleet

## Use case

'Post to LinkedIn, Instagram, YouTube via Composio.'

## How to use it

Follow the skill body step by step; verify with tool output before handover.

## Pitfalls

- `LINKEDIN_CREATE_LINKED_IN_POST` `images[]` accepts Composio `s3key` references only — a registered LinkedIn asset URN cannot be attached through it, so post text+link when no staged file exists.
- LinkedIn exposes no API for background banners, Featured slots, or profile edits — feed posts only; pinning to Featured is always a manual user tap, so say so instead of implying automation.
- PRIVATE-typed Instagram accounts may still publish, but if Graph rejects with auth/permission errors the fix is Business/Creator conversion, not retries.
- For Reels, `video_url` values with query parameters are rejected (OAuthException 2207076) — use the clean direct file URL.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/social-media-publishing/`). Video walkthrough ships with the pack.
