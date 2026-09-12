# narration-tracks

**Pack:** content-fleet

## Use case

'Use when producing per-section TTS narration MP3s for video.'

## How to use it

## Procedure

1. Write one `narration-<section>.txt` per section covering 100% of the script beats. Write TTS-safe: spell acronyms with hyphens (`C-R-M`, `A-P-K`), expand symbols (`percent` for `%`), no slashes or markup.
2. Render each section to MP3 (128k). Keep engine, voice, rate/pitch settings identical across sections.
3. Verify every file before claiming success: `ffprobe` duration per file and summed total, plus `ffmpeg -af volumedetect` mean/max volume proving real speech (around -20 dB mean, peaks near -2 dB). File existence alone proves nothing.
4. Write `VOICE-REPORT.md` in the same folder: engine + version, voice design (speaker/preset, instruct prompt, why chosen), per-file durations, total runtime, word count, quality vs the baseline voice, and assembly order.
5. Log the work to the governing Multica issue the same turn (comment + status), per the multica-workspace skill.

## Pitfalls

- Never fake audio: if an engine fails, record the exact error in the report and fall back openly (closest voice, same scripts) — a missing file must be stated, never implied.
- Do not inflate runtime by padding scripts: report `words to minutes` honestly and flag when the source beats under-specify the target runtime, so expansion is a deliberate choice, not invented content.
- edge-tts reads script files with `--file` — there is no `--text-file` flag; the wrong flag fails with a usage error and renders nothing, so check output files exist before reporting success.
- Darren's preferred narrator engine is OpenAI gpt-4o-mini-tts with an energetic pro-YouTuber instruction prompt — beats flat edge-tts GuyNeural for hooklines. User shorthand: 'punchy tts' means this OpenAI voice, 'old tts' means flat edge-tts — if he says 'back to punchy tts', re-render with OpenAI and re-time captions to the new audio, never edge. Qwen3-TTS is non-viable on his 8 GB Windows PC (model won't load, local disk pressure), so default to OpenAI for narration and treat edge-tts as a backup voice only.
- Verify the voice before assembling the video: when Darren asks for 'high quality' or 'catchy', deliver a 2–3 voice shootout (OpenAI variants + edge) and let him pick — never hand him the final cut in an unchosen voice.
- See `references/voice-design.md` for Qwen3-TTS model/speaker notes (smallest runnable model, speaker picks, CPU install recipe, flaky-download fix).
- Keep sections independent (own txt + own MP3 + own duration row) — re-rendering one section must never require touching the others.
- Deliver voice picks as playable in-chat audio for A/B choice, never as md/doc attachments — Darren decides by listening, and doc files cost him a context switch he will not make.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/narration-tracks/`). Video walkthrough ships with the pack.
