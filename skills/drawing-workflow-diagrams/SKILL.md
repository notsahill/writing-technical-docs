---
name: drawing-workflow-diagrams
description: Use when asked for a high-level workflow diagram, HLD visual, architecture diagram, system flow, sequence-of-handoffs picture, swimlane, or state machine that will be delivered as an Artifact page
---

# Drawing Workflow Diagrams

## Overview

A workflow diagram earns its place when it shows a **mechanism** a reader would otherwise assemble from prose: where data flows, which components talk, what state a request moves through, what stops it. Boxes named after nouns say less than the prose. Label every arrow.

This skill ships one opinionated visual system, so every diagram you produce reads as one family, plus the layout geometry and inline-SVG mechanics that keep a hand-authored diagram from falling apart. The palette and typefaces are a working default — see **Make it yours**. The encodings and the geometry are the half worth keeping whatever colours you land on.

It does not replace design judgement about content: what to draw is still your call.

**REQUIRED BACKGROUND:** load `artifact-design` and `artifact-diagramming` before writing the file — this skill supplies the palette, type, and layout kit those two leave open.

## When to Use

- "draw the workflow", "create a workflow diagram", "diagram the architecture", "show me the flow"
- An HLD / design review that needs a picture of services, queues, tables, and handoffs
- A lifecycle or state machine (document status, order status, job retries)

**Do not use for:** a single 3-box relationship a sentence covers; charts of quantitative data (use `dataviz`); UI mockups (use `design`).

## The style

**Encodings are the non-negotiable half** — what solid, dashed, accent and amber *mean* must stay consistent, or the reader has to relearn the diagram every figure. The specific hues and typefaces are a default you can swap.

Copy `diagram-kit.css` into the page's `<style>` verbatim, then build on it. Start each figure from `lane-skeleton.svg` — it encodes the geometry contract below.

| Element | Choice |
|---|---|
| Palette | Greenbar continuous-form paper. Ground `#F2F4EF`, accent `#2E6B4C` (success path), amber `#94590F` (failure/exit), neutrals biased green |
| Type | IBM Plex Serif 600 headings · IBM Plex Sans body · IBM Plex Mono every label, table, caption, and word inside an SVG |
| Node encoding | solid = service component · dashed = external system · accent-filled = datastore table · amber-filled = terminal outcome |
| Edge encoding | accent solid = success path · amber dashed = failure/retry · neutral thin = read/write/side call |
| Figure chrome | `<figure>` card, 1px rule border, radius 10, `overflow-x:auto`, `<figcaption>` in mono stating one claim |
| Sections | Kicker (mono, uppercase, owning-service or scope) → serif `h2` → one prose line → figure. **Never number the sections unless the content is a real sequence.** |

Themes: define the full light palette on bare `:root`, redefine tokens under both `@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) }` and `:root[data-theme="dark"]`. `.dg { color: var(--ink) }` so `currentColor` carries through the SVG.

## Make it yours

Three edits rebrand every diagram this skill produces. Nothing else needs touching.

1. **Colours** — replace the ten values in `diagram-kit.css` under bare `:root`, then the same names in both dark blocks (`@media (prefers-color-scheme: dark)` and `:root[data-theme="dark"]`). Keep the token *names*; every rule downstream reads them.
2. **Typefaces** — swap the Google Fonts `<link>` and the `--sans` / `--serif` / `--mono` tokens. Keep a real fallback stack on each.
3. **Nothing else.** The `.dg` classes, the geometry contract and the encodings are palette-independent.

Two rules survive any rebrand, because they carry meaning rather than taste:

- `--accent` means success or the deterministic path. `--amber` means failure, retry or a terminal exit. Never spend either on decoration.
- Neutrals should carry a slight hue bias toward your accent. A pure mid-grey reads as unconsidered.

The shipped default is greenbar continuous-form paper — the green-barred stock old line printers used for financial reports — with IBM Plex for its payroll-and-mainframe lineage. It suits systems and data subjects. It will fight a consumer brand, so swap it rather than forcing it.

## Layout recipe

**Swimlane, one lane per owner.** Left rail (x 0–96) carries the lane name + runtime in mono, right-anchored. Content starts at x=112. Lanes are separated by dashed hairlines and an alternating `fill: currentColor; opacity: .035` band — **never** a saturated band, because connectors cross them.

Per lane, two rows:
- **row 1** (y+24, h=50): the flow, left → right, 4–6 nodes, `gap 20–26px`
- **row 2** (y+116, h=44): what each row-1 node touches — external systems and datastore chips, positioned *directly under their owner*
- the remaining ~80px is the **gutter**: reserved for the handoff line into the next lane and for failure loops. Keep it empty of nodes.

Put a queue or broker node **straddling the lane divider** — it is the boundary, and drawing it there says so.

Node text = title (`.t`, sans 13) + one qualifier (`.t2`, mono 10). Two lines, no more.

## Geometry gotchas (all cost a rewrite if missed)

1. **Plan every polyline before writing it.** Long connectors are what break; nodes are easy. Route in gutters and reserve x-corridors.
2. **A vertical from a node to a table chip must clear row 2.** If row 2 is occupied under that node, move the chip, not the arrow.
3. **Don't draw one shared datastore strip at the bottom** — arrows from the top lane have to cross every lane to reach it. Put table chips inside each lane's row 2 instead.
4. Failure loops run at a single y in the gutter, forked from one drop point. One label per segment, sitting ~8px above the line.
5. **No `<style>` or `<script>` inside the SVG.** Style via page CSS classes on the SVG elements. Arrowheads are `<marker>`s whose `<path>` carries a class (`.ah`, `.ah-a`, `.ah-w`) — `context-stroke` is not reliable enough.
6. Give each figure its own marker ids (`a1`, `a1a`, `a1w`, then `a2…`) — ids are document-global and collide across figures.
7. `viewBox` sized to content; `.dg { width:100%; height:auto; min-width:720px }` and the `<figure>` scrolls.
8. Mono at 10px ≈ 6px/char, 13px sans ≈ 6.6px/char. Check label width against its slot before committing.

## Page composition

1. Masthead: mono eyebrow, serif `h1` (2–4 word product-style name), one-sentence deck, a `.facts` rule-bounded row of counts.
2. One legend row of chips — the encoding, once, for all figures.
3. **The end-to-end figure** (the deliverable), then 1–2 zoom-ins that each show a *different* mechanism (state machine; delivery/retry semantics). Do not zoom in on something the master figure already says.
4. `.notes` definition list after a figure for the 2–3 facts the picture can't carry.
5. Source map table: file path → step in the workflow. Engineers open this first.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Unlabeled arrows | Every edge gets a verb or a payload: `INSERT`, `long-poll`, `{document_id, model}` |
| A node per class/file | Nodes are steps and systems, not types. Fold detail into `.t2` |
| Numbered section markers on non-sequences | Label by owning service or scope instead |
| Restating the master figure in figure 2 | Each figure = one distinct claim, stated in its `figcaption` |
| Colour used decoratively | Accent and amber mean success and failure. Nothing else may take them |
| Emoji as section markers, centered everything, gradient hero | House style is left-aligned, rule-separated, quiet |

## Verify before publishing

- [ ] Every arrow labelled; every colour carries meaning
- [ ] No line passes through a node or another label
- [ ] Marker ids unique per figure; no `<style>` inside any SVG
- [ ] Each SVG has `role="img"` + an `aria-label` carrying the same claim as its caption
- [ ] Both themes checked: no colour defined only inside a media or `[data-theme]` block; `body` background comes from a token
- [ ] Content verified against the code, not the docs — read the source paths listed in the source map
