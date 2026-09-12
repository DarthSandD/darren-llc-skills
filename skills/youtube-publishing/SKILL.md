---
name: youtube-publishing
description: 'Use when publishing videos to YouTube via Composio.'
---

# YouTube Publishing (Composio)

Publish a local video file to an owned YouTube channel and brand it end to end.

## Procedure

1. Baseline the channel with YOUTUBE_GET_CHANNEL_STATISTICS (part snippet,statistics,brandingSettings) — record title, description, and videoCount so branding edits carry every field forward.
2. Stage the file: Composio upload tools accept only their own s3key, never a local path or bare URL. Download the file into the REMOTE_WORKBENCH sandbox from a public URL, then call `upload_local_file` and capture the returned s3key. Confirm the staged byte count matches the local file before proceeding.
3. Upload with YOUTUBE_MULTIPART_UPLOAD_VIDEO (title max 100 chars, description, tags, categoryId, privacyStatus) and persist the returned videoId.
4. Brand: pin the video as channel trailer via YOUTUBE_UPDATE_CHANNEL (unsubscribedTrailer), rewrite the description in pro format (top link block, stack, walkthrough paragraph, contact, timestamps, hashtags), and set the thumbnail via YOUTUBE_UPDATE_THUMBNAIL with a public JPG/PNG under 2MB.
5. Verify every write from the response payload (snippet/brandingSettings read-back) — never claim published from a success flag alone.

## Pitfalls

- Default-urllib fetches get 403 on hardened hosts — send browser User-Agent plus Referer headers when pulling a file into the sandbox, or the download dies before staging starts.
- Custom-thumbnail 403 means an unverified channel — hand the owner the youtube.com/verify link and continue everything else. If the owner confirms verification and the 403 persists, it is propagation/API lag, not your payload: wait, retry once, then fall back to YouTube Studio manual upload instead of looping.
- YOUTUBE_UPDATE_CHANNEL brandingSettings.channel is authoritative: send the complete payload (title, description, keywords, country, defaultLanguage) or omitted fields get cleared.
- A sooner-published public watch page doubles as the staging source: publish the mp4 to a live URL first, then pull it into the sandbox from there.
- If the Composio MCP server goes unreachable mid-workflow, wait out the cooldown (~60s) and retry once before replanning — transient, not a dead end.
