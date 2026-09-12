# fleet-video-pipeline

**Pack:** content-fleet

## Use case

Use when producing a Fleet School build video end-to-end.

## How to use it

## Procedure

1. **Script** in 4 beats (hook, step1-3), ELI5, hook-first. Save to
   `hermes-work/video-N/script.md`.
2. **Voice**: Edge (Christopher) for timing drafts; OpenAI Voice A for the
   shipped final only. Per-section MP3s, note pacing.
3. **Cards**: navy/yellow fleet visuals, one per beat + hook card.
4. **Assemble** with ffmpeg 720p locally (never FAL video units). Verify
   duration/size with tool output before publishing.
5. **Distribute same turn**: YouTube upload + description, watch page,
   Discord #build-videos post, GitHub repo Pack folder.
6. **Log Multica** with video ID + URLs.

## Pitfalls

- Cost split is load-bearing: Edge drafts + Voice A finals cuts voice spend
  ~80%. Never ship Edge voice on the channel (video bar: punchy OpenAI voice).
- Every take has motion; YT gets clean video + sidecar SRT.
- Custom thumbnails need channel phone verification — retry only after user
  confirms youtube.com/verify.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/fleet-video-pipeline/`). Video walkthrough ships with the pack.
