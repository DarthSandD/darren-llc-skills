---
name: indonesia-marketplace-sourcing
description: "Use when sourcing electronics BOMs on Tokopedia Indonesia."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Shopping, Electronics, Indonesia, Tokopedia, BOM]
    related_skills: []
---

# Indonesia Marketplace Sourcing

Source DIY electronics BOMs Tokopedia-first for users in Indonesia who prefer it over Shopee.

## When to Use

- User shares a maker/YouTube build (e.g. ESP32 voice assistant) and asks for parts in Indonesia.
- User says Shopee is bad / wants Tokopedia-only links.
- Any ESP32 + sensor + audio + power BOM destined for Tokopedia checkout.

## Procedure

1. Split the BOM into tiers before searching: must-have core (USB-powered desktop V1), then portable add-ons (battery + charger + switch), then optional (display, frame, tools). Confirm desktop-V1 vs portable with the user before building the full cart — never assume the full replica is wanted.
2. Search Tokopedia-first per part with `web_search` using `site:tokopedia.com <part> <spec>` queries and always pass an explicit non-empty `query` string. Collect one search-result (`tokopedia.com/find/...`) link plus one specific product link per part; quote prices as ~Rp ranges since listings move.
3. Pin variant-critical specs in the answer: ESP32-C3 for Xiaozhi-class firmware (not S3/C6 unless the firmware supports it), OLED 0.96in SSD1306 I2C 4-pin 128x64, INMP441 I2S mic (not analog modules), MAX98357A I2S amp (not PAM8403), TP4056 Type-C with protection, LiPo 103040 3.7V with protection, mini slide switch 3-pin.
4. State honestly what Tokopedia does not carry instead of forcing a substitute: craft brass tube 1.5mm / copper wire 1mm and similar maker-frame stock are generally absent — recommend breadboard / plastic box / 3D print for V1 and keep the art frame optional.
5. Deliver Tokopedia-only when asked: bullets with bold part names, one search link plus 1-2 specific product links per part, each link on its own line for mobile tapping, no tables, concise token-conscious wording.

## Pitfalls

- Sourcing the full replica BOM before confirming the tier — the desktop-V1 vs portable decision halves the cart, so ask first.
- Accepting a close-title listing with the wrong variant (analog mic, wrong amp, wrong ESP32-C) — verify the module suffix in the spec, not just the keyword.
- Offering the oversized integration board (e.g. ESP32-S3 mic+amp combo module) as a drop-in for discrete I2S parts — flag it as oversized and exclude it unless the user wants it.
