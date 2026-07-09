# Pure Vue · the new stack — Presentation

A Reveal.js decision deck about moving our frontend stack from **Nuxt (SPA
mode)** to **pure Vue 3 + Vite**:

1. **UI framework** — Nuxt UI vs Vuetify vs PrimeVue vs shadcn-vue, scored
   against our use case (pure Vue, RTL/Farsi/Jalali, design tokens,
   agent-driven development). The scorecard favors Nuxt UI v4 standalone —
   presented as a proposal, decided in the meeting.
2. **Component classification** — our four layers (`Base*` / `App*` / module
   components / screens), the placement decision tree, and a comparison with
   the modern component hierarchy (tokens → primitives → composites →
   patterns → features → pages; adopt / hybrid / keep ours — an open
   decision).
3. **Where logic lives** — composables vs Pinia stores vs utils vs module
   use-cases, with the ordering rules and real examples.
4. **Labs & design tokens** — `tokens.css` as the single source of truth and
   the Labs screen as the living component catalog.

The deck **defines and compares only** — every decision is made live in the
team meeting; nothing is pre-decided.

Slides are **English and intentionally low-text**; the full narration and
storytelling lives in [`STORY.md`](./STORY.md) (**Farsi**), and is mirrored
into each slide's speaker notes (press `s`).

The deck is **CDN-only** — no build step, no `node_modules`. Every dependency
loads from `cdn.jsdelivr.net` (fonts from `fonts.bunny.net`). Push to GitHub
and Pages serves the static files directly.

## View locally

```sh
python3 -m http.server 8080
# then open http://localhost:8080
```

Reveal keys: `→ ←` to navigate, `?` for help, `s` for speaker notes,
`f` for fullscreen, `Esc` for the slide overview.

## Edit slides

Read `.claude/skills/deck-authoring/SKILL.md` first — it covers the slide
text budget, the Mermaid-in-Reveal lazy-render fix, the styles architecture,
and the rule that every slide keeps a matching `STORY.md` entry.

Each `<section>` in `index.html` is one slide. Code blocks use
`<pre><code class="language-…">…</code></pre>` (Monokai highlighting); flow
diagrams use `<pre class="mermaid">…</pre>`.

## Deploy

`.github/workflows/pages.yml` publishes the static files to GitHub Pages on
every push to `main`. One-time setup:
**Settings → Pages → Build and deployment → Source: GitHub Actions**.

## Layout

```
.
├── index.html              # the deck (30 slides, 5 acts)
├── styles.css              # theme overrides (all design tokens in :root)
├── STORY.md                # Farsi narration & storytelling, per slide
├── .claude/skills/deck-authoring/SKILL.md
├── assets/                 # images / SVG (placeholder)
├── .github/workflows/pages.yml
├── .nojekyll               # disable Jekyll on Pages
└── LICENSE                 # MIT
```

## License

MIT
