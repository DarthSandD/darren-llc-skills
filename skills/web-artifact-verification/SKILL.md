---
name: web-artifact-verification
description: "Use when handing over generated HTML. Verify it runs first."
version: 1.0.0
---

# Web Artifact Verification

A generated single-file HTML app with embedded JS is not done when written. Prove it runs
before handing it over, cheapest checks first.

See `references/static-site-triage.md` when the artifact ships to static hosting
and whole features fail there while passing locally.

## Procedure

1. **Syntax** — extract the `<script>` block and run `node --check` on it.
2. **ID cross-check** — collect every `getElementById` / `$()` target in the JS and assert each
   exists as an `id=` in the markup. A referenced-but-missing ID is the most common silent
   killer in generated pages: no error, just a dead button.
3. **Execute the logic headlessly** — stub a minimal DOM in Node (elements exposing
   `value`, `innerHTML`, `classList`, `addEventListener`), seed inputs with the page's own
   defaults, invoke each handler, and print the outputs. Prefer this over a live browser for
   logic bugs; it is faster and scriptable.
4. **Validate ported formulas against one hand-computed case each** — when calculation code is
   ported from another source, check at least one result per formula by hand. Scaled
   engineering units are the classic failure: an input labelled ×10³ mm³ must enter SI as
   ×10⁻⁶ m³, and getting the exponent wrong shifts the answer by exactly 10³ while the code
   looks correct.
5. **Visual check last** — screenshot in a real browser only after steps 1–4 pass. Logic bugs
   outnumber styling bugs and are cheaper to catch headlessly.
6. **Poll the live URL after deploying** — hosts like GitHub Pages rebuild minutes behind
   the push, so the first fetch serves the old bundle; loop (fetch, grep for the new
   bundle hash, sleep) until the new build serves before claiming anything is live —
   verifying the old build proves nothing about the fix.

## Pitfalls

- Fix the artifact, not the harness: when a smoke test reports absurd output, suspect the
  generated code first (unit conversions, stub-seeded defaults), because a stub faithfully
  propagates whatever the code computes.
- Seed the stub with the page's own default input values, not invented ones — invented
  inputs that happen to pass prove nothing about what the user sees on load.
- Check asset paths against the real deploy subpath: a root-absolute `/img/x.svg` 404s on
  any site served under a subpath (GitHub project Pages serve at `/repo/`, not `/`), so
  prefer relative paths or base-relative URLs for every icon, fetch, and worker URL —
  one absolute path silently kills one button while the rest of the page looks fine.
