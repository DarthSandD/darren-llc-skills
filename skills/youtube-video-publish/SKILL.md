---
name: youtube-video-publish
description: Publish videos to YouTube and brand the channel.
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [youtube, publishing, video]
    category: productivity
---

# YouTube Video Publishing

Publish a video to YouTube and brand the channel the way Darren wants: professional description with contact links, proper tags, custom thumbnail, correct visibility.

## When to Use

- User says "post this video", "upload to YouTube", "make the channel look pro", "publish the video"
- User wants to brand a YouTube channel with description, links, keywords
- User wants to set a custom thumbnail on a video

## Procedure

1. **Brand the channel first** — before uploading, make the channel look professional:
   - Use `YOUTUBE_UPDATE_CHANNEL` to set:
     - `brandingSettings.channel.description` — professional description with what the channel is about, what viewers will get, and contact/work-with links
     - `brandingSettings.channel.keywords` — space-separated tags for the channel
     - `brandingSettings.channel.country` — two-letter country code
     - `brandingSettings.channel.defaultLanguage` — BCP-47 language code
   - Get the channel ID first via `YOUTUBE_GET_CHANNEL_STATISTICS` with `mine: true` or `forHandle` if needed
   - Write a description that leads with the channel's value proposition, includes WHAT YOU WILL GET section, and ends with contact/work-with links

2. **Prepare the video file** — the upload tools need a properly formatted local file:
   - Video must be MP4 (or other YouTube-supported format)
   - Use `ffprobe` to verify duration and format before uploading
   - Ensure the file is accessible at a local path on this machine

3. **Upload the video** — use ComposIO YouTube tools:
   - `YOUTUBE_MULTIPART_UPLOAD_VIDEO` for single-request upload (metadata + file together)
   - `YOUTUBE_UPLOAD_VIDEO` as fallback
   - Required fields: `title`, `description`, `tags`, `categoryId`, `privacyStatus`, `videoFile` (multipart) or `videoFilePath` (resumable)
   - `categoryId` "22" for People & Blogs
   - `privacyStatus`: "public" for immediate publish, "unlisted" for QC first
   - Tags as an array of strings, max 500 chars total

4. **Set the thumbnail** — after upload completes:
   - Use `YOUTUBE_UPDATE_THUMBNAIL` with the video ID and a public image URL
   - Thumbnail must be JPG/PNG/GIF, under 2MB, 1280x720 recommended (16:9)
   - The channel must be phone-verified to upload custom thumbnails

5. **Verify the upload** — confirm the video is live:
   - Use `YOUTUBE_GET_VIDEO_DETAILS_BATCH` with the video ID
   - Check that `status.privacyStatus` matches what was set
   - Check that `snippet.title` and `snippet.description` are correct
   - Report the video URL to the user

## Pitfalls

- **ComposIO YouTube uploader needs staged S3 keys** — the uploader (`YOUTUBE_MULTIPART_UPLOAD_VIDEO` / `YOUTUBE_UPLOAD_VIDEO`) cannot fetch from external URLs like here.now R2 storage or direct HTTP links. When upload fails with "storage returned HTTP 404" or "file may not exist", the `videoFile.s3key` / `videoFilePath.s3key` is not a supported staged path. Fix: download the media locally first and pass the local path, or publish a watch page via here.now as an interim share while the upload path is resolved.
- **Thumbnail set requires phone-verified channel** — `YOUTUBE_UPDATE_THUMBNAIL` will fail with HTTP 400 if the channel is not phone-verified. Verify the channel in YouTube Studio first if thumbnail setting fails.
- **Video processing is async** — immediately after upload, `YOUTUBE_GET_VIDEO_DETAILS_BATCH` may return empty or partial results while YouTube processes the file. Poll every 10–20 seconds with a hard cap until the video is retrievable.
- **Tags character limit** — total characters across all tags must not exceed 500. Angle brackets are stripped automatically.
- **Description length** — video description max 5000 bytes, not characters. UTF-8 multibyte characters count more than ASCII.

## References

- `references/channel-branding.md` — channel description templates and keyword guidance
- `references/upload-failures.md` — common upload failures and fixes
