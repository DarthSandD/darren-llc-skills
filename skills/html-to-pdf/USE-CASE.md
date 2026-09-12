# html-to-pdf

**Pack:** ship-deploy

## Use case

Render HTML to PDF via headless browser; layout matters.

## How to use it

## Procedure

### 1. Verify the HTML source renders the way you expect

Open the HTML in a browser (or via `browser_exec`) and eyeball it
before rendering. Catch layout problems early — a PDF of a broken
page is still a broken page.

### 2. Render to PDF with Playwright

```python
from playwright.sync_api import sync_playwright
import os

HTML_FILE = 'resume.html'   # change to your file
PDF_FILE  = 'resume.pdf'

with sync_playwright() as p:
    browser = p.chromium.launch(
        channel='chrome',
        args=['--no-sandbox', '--disable-dev-shm-usage']
    )
    page = browser.new_page()
    page.goto('file://' + os.path.abspath(HTML_FILE))
    page.wait_for_load_state('domcontentloaded')
    page.pdf(
        path=PDF_FILE,
        format='A4',
        print_background=True,
        margin={
            'top': '14mm',
            'bottom': '14mm',
            'left': '15mm',
            'right': '15mm',
        }
    )
    browser.close()

size = os.path.getsize(PDF_FILE)
print(f'PDF created: {PDF_FILE} ({size/1024:.1f} KB)')
```

Run it with the Hermes venv Python, **not** the system `python3` on
PATH:

```bash
"C:/Users/USER/AppData/Local/hermes/hermes-agent/venv/Scripts/python.exe" \
    render.py
```

### 3. Tune page margins and page size in CSS or in `page.pdf()`

- For A4 with normal margins, the `margin` dict above is a good default.
- For full-bleed or custom paper sizes, set `format` to a known size
  (`'Letter'`, `'A3'`, `'A4'`, `'A5'`, `'Legal'`) or pass
  `width`/`height` in CSS pixels.
- Print CSS (`@page { size: A4; margin: 14mm; }`) in the HTML also
  drives the render — Playwright honors it.

### 4. Verify before handing over

- Confirm the file exists and is non-trivial in size (a blank page is
  usually under 10KB).
- For layout-critical documents (resumes, CVs), render page 1 to PNG
  and inspect:

  ```bash
  "C:/Users/USER/AppData/Local/hermes/hermes-agent/venv/Scripts/python.exe" \
      -c "
  from playwright.sync_api import sync_playwright
  with sync_playwright() as p:
      b = p.chromium.launch(channel='chrome')
      pg = b.new_page(viewport={'width': 1200, 'height': 1600})
      pg.goto('file:///path/to/resume.html')
      pg.screenshot(path='resume-page1.png', full_page=False)
      b.close()
  "
  ```

  Then call `vision_analyze(image_url='resume-page1.png', question=
  'Is this resume clean, readable, and professional? Any overflow,
  cutoff text, broken layout, or misaligned sections?')` and fix
  before delivering.

- For multi-page documents, also check the last page for orphaned
  headings or awkward breaks.

## Pitfalls

- **Do not use the system `python3` (3.14) to run playwright scripts.
  Install `playwright` into the Hermes venv Python (3.11) instead.**
  The system interpreter's site-packages may not resolve cleanly and
  can collide with uv-managed installs. The venv Python is the one
  that already has playwright working.
- **`playwright install chromium` is slow and often unnecessary** when
  `channel='chrome'` works. Only run the install if Chrome is absent
  or channel='chrome' fails. Prefer `channel='chrome'` first.
- **WeasyPrint is not a usable fallback on this Windows host.** It
  installs but fails at import time trying to load GTK libs
  (`libgobject-2.0-0`, `libpango`) that are not present. Do not
  recommend WeasyPrint as a Windows fallback — go straight to the
  headless browser.
- **The browser-harness daemon must see Chrome running.** If
  `browser_exec` or a playwright launch fails with
  "no supported Chromium-family browser is running", start Chrome
  manually once, then retry. This is a daemon-state issue, not a
  Playwright bug.
- **`page.pdf()` honors the page's rendered CSS, including `@page`.
  If margins look wrong, check both the `margin` argument to
  `page.pdf()` and any `@page` rule in the HTML — they interact.**
- **Screenshots for verification use `full_page=False` by default.
  For a full-page capture set `full_page=True`, but be aware that
  tall pages produce very large PNGs.**
- **Do not claim a PDF is good without checking it visually for
  layout-critical documents.** A 60KB PDF can still have cutoff text
  or overflowing containers — file size is not a layout guarantee.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/html-to-pdf/`). Video walkthrough ships with the pack.
