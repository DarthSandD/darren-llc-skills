# linkedin-launch-kit

**Pack:** growth-money

## Use case

Build LinkedIn launch kits: post draft plus cover image.

## How to use it

## Procedure
1. **Research first, write second.** Read the source before drafting: repo README, code, live URL output. Never draft from the request text alone. Each kit carries a SHIPPED-or-WIP verdict with one-line evidence — portfolio shows finished work only, so flag WIPs instead of launching them.
2. **Post format (Darren-approved):** hook-first opening line, short lines, 3–6 emoji max, concrete numbers from verification (not adjectives), live link, ends with a question. Approval-first: draft in chat, Darren posts himself unless told otherwise.
3. **Cover image — AI paints, code writes.** Image models garble on-image text, so never let the model draw words: generate a text-free background (prompt must say no text/words/letters/numbers/logos), then render all typography with the `scripts/cover_overlay.py` script (PIL, real font file). Text-perfect by construction.
4. **Verify before handover:** open the final PNG yourself and read every word; check file size is sane (hundreds of KB, not bytes). Resolve every release/download link live (`gh release view`) — no dead links. Strip any claim you can't prove (e.g. '#1 Trending' on a 0-star repo). If the visual checker is down, say so and ask Darren to eyeball the text — never claim QC you didn't run.
5. **Deliver** one message per project (post text paste-ready) + cover as native attachment. Log to the governing Multica issue same turn.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/linkedin-launch-kit/`). Video walkthrough ships with the pack.
