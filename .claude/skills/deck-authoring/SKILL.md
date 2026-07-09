---
name: deck-authoring
description: >
  How to author, style, and verify a Reveal.js presentation deck in this repo —
  CDN-only setup, slide/text budgets, the Mermaid-in-Reveal lazy-render fix,
  styles architecture, the Farsi STORY.md narration companion, and GitHub Pages
  deploy. Use for any task that adds or edits slides, deck styles, or narration.
---

# Deck authoring

This repo is a **static Reveal.js deck**: no build step, no `node_modules`.
Every dependency loads from `cdn.jsdelivr.net` (and fonts from `fonts.bunny.net`).
GitHub Pages serves the files as-is.

## Files

| File | Role |
| --- | --- |
| `index.html` | The deck. One `<section>` per slide. |
| `styles.css` | Theme overrides on top of Reveal's `black.css`. All design tokens are CSS custom properties in `:root`. |
| `STORY.md` | **Farsi** narration companion — the storytelling the presenter reads. Slides stay English. |
| `.github/workflows/pages.yml` | Pages deploy on push to `main`. |

## Hard rules

1. **CDN-only.** Never add a package.json or bundler. New libraries come from
   jsdelivr with a pinned version.
2. **Text budget per slide:** ≤ 5 bullets, ≤ 12 words per bullet, one idea per
   slide. Anything longer belongs in `STORY.md` and the slide's
   `<aside class="notes">`, not on screen.
3. **Every slide has a matching STORY.md entry** (`## اسلاید N — <title>`) with
   sections **روی صفحه** (what's on screen, one line), **متن ارائه** (Farsi
   narration), **پل** (bridge to the next slide). Keep slide count and numbering
   in sync when adding/removing slides.
4. **Speaker notes mirror the Farsi narration.** Each `<section>` ends with
   `<aside class="notes">…</aside>` containing the same Farsi text (short form ok).
5. **Prefer CSS-rendered visuals over prose:** swatch grids, score dots, layer
   stacks, card walls. A table of words is the last resort.
6. Slide canvas is **1280×720** (`width/height` in `Reveal()` init) — check that
   content fits without scroll at that size.

## Mermaid inside Reveal — the lazy-render fix (do not regress)

Reveal hides off-screen slides with `display:none`; Mermaid measuring labels in a
hidden slide produces NaN layouts and the bogus **"Syntax error in text"**. The
fix (already in `index.html`, keep it verbatim when touching deck JS):

1. `mermaid.initialize({ startOnLoad: false, securityLevel: 'loose', … })`
   **synchronously** at script top so Mermaid's own DOMContentLoaded auto-start
   is disabled.
2. Cache every block's source: `el.dataset.mermaidSource = el.textContent`.
3. Render lazily per slide via `deck.on('slidechanged', …)` +
   `renderMermaidIn(deck.getCurrentSlide())` after `deck.initialize()`.
4. Mark success with `el.dataset.mermaidDone` only when the SVG has no
   `.error-icon`; on failure the source is restored and retried next visit.

Diagram authoring: use `<pre class="mermaid">` blocks, `\n` inside node labels
for line breaks (needs `securityLevel: 'loose'`), and `classDef` colors that
match the deck palette in `styles.css`.

Diagram sizing — keep charts big enough to read from the back of the room:
- Mermaid only scales **down** to fit the container, never up. A wide diagram
  that already fills the slide gains nothing from a larger font — reduce its
  width (shorter labels, `rankSpacing`/`nodeSpacing`) so the bigger font fits.
- Tune each diagram with a first-line init directive instead of the global
  config: `%%{init: {"themeVariables": {"fontSize": "18px"}, "flowchart":
  {"rankSpacing": 44, "nodeSpacing": 38}}}%%`.
- Decision trees read best as `flowchart LR` (720px of height kills TD trees).
  A long LR chain that gets too small should flip to `TB` inside a `grid-2`
  next to its bullet points — narrow diagrams render at full font size.
- Keep the `foreignObject { overflow: visible }` rule and the system
  `fontFamily` in Mermaid config: label boxes are measured at render time and
  webfonts/rounding otherwise clip the last glyph.

## Styles architecture

- All colors/spacing live as custom properties in `:root` of `styles.css` —
  slides reference `var(--…)`, never hex literals inline.
- Layout helpers: `.grid-2/.grid-3/.grid-4`, `.card`, `.tag`, `.lead`,
  `.part-divider` (act separators), `.numbered-grid`.
- Every decision-carrying slide ends with a `.biz` strip — two cells,
  `.gain` (business benefit: maintainability, dev speed, onboarding…) and
  `.cost` (what it costs), one sentence each. Mirror the same point in that
  slide's STORY.md entry (an «از نگاه بیزینس» paragraph).
- Fonts load from `fonts.bunny.net` (GDPR-friendly Google Fonts mirror, still
  CDN-only).

## Verify before finishing

1. `rtk python3 -m http.server 8080` in the repo root, open `http://localhost:8080`.
2. Step through **every** slide (browser MCP or manually): all Mermaid diagrams
   render, no console errors, nothing overflows the 720px canvas.
3. Confirm `STORY.md` entry count == `<section>` count.

## Deploy

Push to `main` → the Pages workflow publishes to
`https://<owner>.github.io/<repo>/`. One-time GitHub setting:
**Settings → Pages → Source: GitHub Actions**.
