# self-serve-fix

**Pack:** system-utils

## Use case

Use when handling something unknown or a learn-this link.

## How to use it

## Procedure

1. **Web search first.** Run 2–3 `web_search` queries in one batch: the thing
   itself, `<thing> alternative / comparison`, and `YouTube <thing>` for video
   coverage.
2. **Pull the primary sources.** `web_extract` the top comparison/guide pages
   (raise `char_limit` when the head+tail cut hides the middle). For any
   YouTube link, fetch the transcript via the `youtube-content` skill script
   — transcript beats description every time.
3. **Present before fixing.** Short brief: what it is, the options that fit
   THIS user (free-first, constrained hardware), named sources. No fluff.
4. **Fix in two layers.** Stage the full solution as files (kit, templates,
   configs) AND apply the safe subset that runs today. A staged-but-unapplied
   fix plus a live subset beats a description of a fix.
5. **Verify with tools.** `ls`, status commands, read-backs — never claim a
   write or switch landed without tool output proving it.
6. **Log Multica the same turn.** Every substantive fix gets a Darren111
   issue comment with what changed and what stays blocked.
7. **Report live-vs-blocked.** What's running now, what needs the user's
   finger (installs, keys, sign-ups), each as a one-tap next step.

## Pitfalls

- Check hardware BEFORE proposing local heavy stacks — `free`/meminfo, disk
  usage, and `docker --version` / CLI presence first. Postgres + LLM + Redis
  or multi-GB models fail on a full 8GB box; stage the kit and ship the
  file-only subset instead of promising a server that can't start.
- If `uv run python` can't see a just-installed package, install with
  `uv pip install --system` and run with plain `python` — the managed venv
  and the runner can resolve to different environments.
- Never `--force-reinstall` into the live Hermes venv — files locked by the
  running gateway make the uninstall succeed and the reinstall fail, leaving
  gutted packages behind. Use `--no-deps` for single-package fixes; repair a
  gutted package by extracting only its missing `.py` files from the PyPI
  wheel over the existing dir, skipping locked binaries.
- Only one Hermes external memory provider is active at a time and built-in
  MEMORY.md/USER.md always runs underneath — a "combo" is one provider plus
  file-backed layers, never two providers. See `references/hermes-memory.md`.
- Transcript caches land under the Hermes web cache; page the saved file with
  `read_file` offsets instead of re-fetching when you need the middle.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/self-serve-fix/`). Video walkthrough ships with the pack.
