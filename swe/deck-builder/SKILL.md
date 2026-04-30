---
name: deck-builder
description: >
  Build PowerPoint (.pptx) decks programmatically from a JSON spec. Use this
  skill whenever the user mentions slides, a deck, presentation, .pptx, pitch
  deck, QBR deck, exec readout, board slides, "build me a slide" — or any
  request that involves producing or modifying a slide deck. Trigger even when
  the user asks for "just text" content for slides, since the natural follow-up
  is usually to render to .pptx.
kit: swe
audience: [swe, ae, csm, flm, exec]
workflow: [drafting, reporting]
tools: []
tags: [slides, deck, presentation, pptx, powerpoint]
source_kind: pendo-curated
author: sean.muse@pendo.io
version: 1.0.0
created: 2026-04-30
---

# Deck Builder

Generate a polished `.pptx` from a single JSON spec. The spec is the source of
truth — write/edit JSON, then run the script to produce the deck. Round-tripping
keeps the deck reproducible and reviewable in code.

## When to use
Trigger on any ask involving slides, decks, presentations, board readouts, QBR
slides, sales decks, customer recaps, or `.pptx` files in any direction (input
or output). Prefer this skill over freeform Markdown when the user expects a
shareable artifact.

## What it does
- Reads a JSON spec describing title, theme, and a list of slides
- Renders each slide using `python-pptx` with sensible layouts (title, two-col,
  bullets, image, quote, section divider)
- Applies a theme — defaults to Pendo magenta (`#EB008B`) on a clean white deck
- Writes a `.pptx` file ready to open in PowerPoint, Keynote, or Google Slides

## Inputs
- `spec` (required): JSON string or path to `.json` file (see schema below)
- `output` (optional): output `.pptx` path (default: `deck.pptx` in cwd)
- `theme` (optional): `pendo` (default) or `mono` (black-on-white)

## Spec schema
```jsonc
{
  "title": "Q4 Customer Health Review",
  "subtitle": "Adobe — 2026-04-30",
  "author": "Sean Muse",
  "theme": "pendo",
  "slides": [
    { "type": "title", "title": "...", "subtitle": "..." },
    { "type": "section", "title": "Adoption" },
    { "type": "bullets", "title": "Pulse signals", "bullets": [
        "MAU up 12% QoQ",
        "Champion (CSweet) confirmed exec sponsor"
      ] },
    { "type": "two-column", "title": "Risk vs save",
      "left": { "heading": "Risk", "bullets": ["..."] },
      "right": { "heading": "Save plan", "bullets": ["..."] } },
    { "type": "quote", "quote": "We can't imagine workflow without Pendo.",
      "attribution": "VP Product, Adobe" },
    { "type": "image", "title": "Renewal timeline", "image": "./renewal.png" },
    { "type": "table", "title": "Health scorecard",
      "headers": ["Metric", "Q3", "Q4", "Δ"],
      "rows": [["MAU", "12k", "13.5k", "+12%"]] }
  ]
}
```

Supported slide `type` values: `title`, `section`, `bullets`, `two-column`,
`quote`, `image`, `table`.

## Instructions

1. Capture the user's content. Ask what they want — the audience (exec, board,
   customer), the message arc, and what artifacts/data they already have. Don't
   guess content; if context is thin, ask one round of clarifying questions.
2. Draft the JSON spec inline in the conversation. Keep slides short — 3-5
   bullets max per slide. Use `section` dividers between major arcs.
3. Save the spec to `<workdir>/deck.json`.
4. Run the build script:
   ```bash
   python scripts/build_deck.py --spec <workdir>/deck.json --output <workdir>/deck.pptx
   ```
5. If the user uploaded an image (logo, chart screenshot), reference its path
   in the slide spec. The script copies it into the deck.
6. After the file is written, tell the user the path and offer follow-ups
   (re-theme, add slides, export to PDF via `soffice --headless`).

## Editing an existing deck
The script can also extract slide content from an existing `.pptx` back to a
JSON spec — useful when the user uploads a deck and wants edits:
```bash
python scripts/build_deck.py --extract <input>.pptx --output <output>.json
```
Edit the JSON, then build again.

## Theme: Pendo brand
- Primary: `#EB008B` (magenta) — title text, accent bars, footers
- Body: `#111111` on `#FFFFFF`
- Font stack: system sans (Calibri / SF Pro Text), no emojis in body content
- Subtle 1pt magenta divider under each title

## Example

User: "Build me a 5-slide QBR deck for Adobe — title, adoption signals,
champion update, expansion plan, ask."

Agent:
1. Drafts JSON with 5 slides matching that arc
2. Saves to `deck.json`
3. Runs the build script
4. Reports `deck.pptx` written, offers to add a renewal-timeline slide

## Output format
- `deck.pptx` — production-quality, openable in any major slide tool
- Optional `deck.json` — preserved alongside for re-runs / version control

## Dependencies
- `python-pptx>=0.6.21` (BSD-3) — install via `pip install python-pptx`
- Optional: `libreoffice` (for `soffice` PDF export) — `brew install --cask libreoffice`
