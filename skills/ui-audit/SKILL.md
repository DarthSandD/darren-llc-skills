---
name: ui-audit
description: "Use when reviewing a page for quality before shipping."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [design, accessibility, ux, review]
    category: creative
---

# UI Audit (from Figma community skills: accessibility-review, anti-ui-slop)

Run before any page ships. Three passes, file-backed, no special tooling.

## Procedure

1. **Anti-slop pass**: one Typeface family max, no default gradients, no
   lorem ipsum, no pill-shaped everything, spacing on an 8px scale.
   Design from DNA (brand tokens), not defaults.
2. **Accessibility pass (WCAG 2.2 lite)**: contrast 4.5:1 body text, every
   image has alt, buttons ≥40px targets, keyboard order matches visual order,
   form inputs have labels.
3. **Usability pass**: one primary CTA per viewport, headline states the
   outcome in 5 seconds, no more than 3 competing colors.
4. **Report**: file + line/selector, severity (blocker vs polish), concrete
   fix. Fix blockers same turn.

## Pitfalls

- Audit the built file, not the plan — read the actual HTML/CSS.
- Contrast numbers come from computed values, never eyeballed.
- A page that fails blockers does not ship, even if the design looks premium.
