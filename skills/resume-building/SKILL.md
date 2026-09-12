---
name: resume-building
description: Write or update Darren's resume, CV, cover letter.
version: 1.0.0
author: Hermes Agent
license: MIT
---

# Resume Building

Author job-application documents for Darren (resume, CV, cover
letter): assemble verified content, apply his house style, render via
`html-to-pdf`, QA visually, hand over a file.

## Content assembly

1. Pull identity basics from LinkedIn (`LINKEDIN_GET_MY_INFO`:
   name, headline, email, profile URL) — never retype what the API
   returns.
2. Ask for dates ONCE, up front (role start/end per job, degree
   status). Never invent dates and never ship `[placeholder]` dates
   in a delivered file — a placeholder that reaches Darren is a
   failed turn.
3. Keep every role to 1–2 bullets, results-flavored
   ("turned a 2-month internship into a full engineering role",
   not "responsible for CI activities").
4. Always include the Selected Builds section with live links
   (Omni Eyes View, VoltWorks Power, JARVIS) — proof-of-shipping is
   the differentiator.
5. Experience is reverse-chronological, newest first. No exceptions.

## House style (standing Darren preferences)

- Tagline is short: `Power Systems Engineer | AI Builder`. No
  trailing buzzword chains.
- No filler adjectives: never "operator", "ninja", "guru",
  "passionate thought leader". Plain nouns for what he does.
- Profile is 2 lines max, not a wall of text.
- Skills render as pills/chips, not a comma blob.
- Voice is natural spoken cadence, never template-ese: "went from intern to process owner in two months", not "responsible for CI activities". Read every bullet aloud — if it sounds like a form, rewrite it.
- Visual identity: navy `#0a2540` header band, gold `#ffc400`
  accents. Starter file: `templates/resume-navy-gold.html` — copy
  it, fill it, do not redesign from scratch each time.
- Education always lists both lines: Bachelor of Management
  (in progress) — Universitas Terbuka, plus the BLK Serang
  (Kemnaker) vocational training.

## Build pipeline

1. Copy the starter template to the working file and fill content.
2. Render with the `html-to-pdf` skill (Hermes venv Python +
   `channel='chrome'`), then screenshot page 1 and `vision_analyze`
   it for overflow/cutoff before handing over.
3. Deliver the PDF as a native attachment (`MEDIA:<abs path>`). Work silently through render → QA → attach and reply once with the file — no progress narration mid-build.
4. Log the work on the matching Multica issue same turn.

## Canva handoff

- Follow the `html-to-pdf` Canva pre-check before promising
  anything inside Canva. Magic Design has no API endpoint.
- If the user says "do it for me" after being offered options,
  execute the recommended path (local designed PDF) without
  re-asking — the delegation IS the answer.

## Pitfalls

- Shipping a file with unfilled date/degree placeholders burns
  trust faster than shipping late — verify every field is real
  before attaching.
- Overlapping timelines (e.g. VTC training overlapping the CI
  internship) are fine if true to Darren's history; do not
  silently "fix" overlaps by inventing dates — ask.
