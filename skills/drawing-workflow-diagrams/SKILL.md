---
name: drawing-workflow-diagrams
description: Use when drawing a figure that shows a mechanism prose cannot — a system or request flow, a swimlane of handoffs between services, a state machine or lifecycle, a retry or failure path, or a before/after of two designs. Covers the figure itself, whether it stands alone or sits inside a larger document
---

# Drawing Workflow Diagrams

## Overview

A diagram earns its place when it shows a **mechanism** a reader would otherwise assemble from prose: where data flows, which components talk, what state a request moves through, what stops it. A box labelled "cache" says less than the sentence it replaced; the path a request takes through it says what words can't.

This skill supplies the layout geometry, the encodings, and the inline-SVG mechanics that keep a hand-authored diagram from falling apart. What to draw is still your call.

**REQUIRED BACKGROUND:** load `artifact-diagramming` before writing any SVG.

**This skill is the figure, not the page.** Palette, type, `doc-kit.css` and page composition belong to `writing-technical-docs` — load that for the document, this for the picture. A request to *document* an architecture is that skill's; a request to *draw* one is this skill's, and it usually arrives via that skill handing off. `diagram-kit.css` here holds only the `.dg` classes and reads the other file's tokens.

## When to Use

- "draw the workflow", "diagram the architecture", "show me how a request flows"
- An HLD or design review needing a picture of services, queues, tables and handoffs
- A lifecycle or state machine (document status, order status, job retries)
- A before/after of two designs — draw the difference, not two disconnected boxes

**Do not use for:** a three-box relationship a sentence covers; charts of quantitative data (use `dataviz`); UI mockups (use `design`).

## Where the diagram lands

Check the destination before drawing — it decides the format, and one of these is a hard platform limit.

| Destination | Format |
|---|---|
| **Artifact page or any HTML** | Hand-authored inline `<svg>`, per everything below |
| **A Markdown file in the repo** | GitHub and most Git hosts **strip `<svg>` from Markdown**. Use a ` ```mermaid ` fence, or commit a standalone `.svg` and reference it as an image |

The encodings and "label every arrow" hold in every format. The geometry contract and the SVG mechanics below are specific to hand-authored inline SVG — a mermaid diagram gets its layout from the renderer, so spend the effort on labels and on drawing only what the argument turns on.

## Encodings

Fixed, because a reader who has to relearn the key every figure isn't reading the diagram.

| Mark | Means |
|---|---|
| Solid node | A component inside a service you own |
| Dashed node | An external or managed system |
| Accent-filled node | A datastore — table, index, bucket |
| Amber-filled node | A terminal outcome, or something being removed |
| Heavy accent outline | New — does not exist yet |
| Accent solid edge | The success or deterministic path |
| Amber dashed edge | Failure, retry, redelivery |
| Neutral thin edge | A read, a write, a side call |

**Label every arrow.** An unlabelled arrow means "related somehow". `INSERT`, `long-poll`, `{document_id, model}` are information.

## Layout recipe

**Swimlane, one lane per owner.** Left rail (x 0–96) carries the lane name and runtime in mono, right-anchored. Content starts at x=112. Lanes are separated by dashed hairlines and an alternating `fill: currentColor; opacity: .035` band — **never** a saturated band, because connectors cross them.

Per lane, two rows:

- **row 1** (y+24, h=50): the flow, left → right, 4–6 nodes, gap 20–26px
- **row 2** (y+116, h=44): what each row-1 node touches — external systems and datastore chips, placed *directly under their owner*
- the remaining ~80px is the **gutter**: reserved for the handoff into the next lane and for failure loops. Keep it empty of nodes.

Put a queue or broker node **straddling the lane divider** — it is the boundary, and drawing it there says so.

Node text = title (`.t`, sans 13) + one qualifier (`.t2`, mono 10). Two lines, no more.

Start from `lane-skeleton.svg`, which encodes this contract.

## Geometry gotchas (each costs a rewrite if missed)

1. **Plan every polyline before writing it.** Long connectors are what break; nodes are easy. Route through gutters and reserve x-corridors.
2. **A vertical from a node to a table chip must clear row 2.** If row 2 is occupied under that node, move the chip, not the arrow.
3. **Never draw one shared datastore strip at the bottom** — arrows from the top lane then cross every lane to reach it. Put table chips inside each lane's row 2.
4. Failure loops run at a single y in the gutter, forked from one drop point. One label per segment, ~8px above the line.
5. **No `<style>` or `<script>` inside the SVG.** Style via page CSS classes on the SVG elements. Arrowheads are `<marker>`s whose `<path>` carries a class (`.ah`, `.ah-a`, `.ah-w`) — `context-stroke` is not reliable enough.
6. Give each figure its own marker ids (`a1`, `a1a`, `a1w`, then `a2…`) — ids are document-global and collide across figures.
7. `viewBox` sized to content; `.dg { width:100%; height:auto; min-width:720px }` and the `<figure>` scrolls.
8. Mono at 10px ≈ 6px/char, 13px sans ≈ 6.6px/char. Check every label's width against its slot before committing.

## Figures on a page

- One legend row of chips states the encoding once, for all figures.
- Lead with the end-to-end figure, then at most one or two zoom-ins that each show a *different* mechanism. Never zoom in on something the master figure already says.
- Every `<figure>` gets a `<figcaption>` stating the one claim it makes, and the `<svg>` gets `role="img"` plus an `aria-label` carrying that same claim.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Unlabelled arrows | Every edge gets a verb or a payload |
| A node per class or file | Nodes are steps and systems, not types. Fold detail into `.t2` |
| Restating the master figure in figure 2 | Each figure makes one distinct claim, stated in its caption |
| Colour used decoratively | Accent and amber mean success and failure. Nothing else may take them |
| Comparing options as two disconnected boxes | Draw the edge each option adds or removes — that's the comparison |
| Forced minimalism on a real mechanism | Draw as much as the decision actually turns on |

## Verify before publishing

- [ ] Every arrow labelled; every colour carries meaning
- [ ] No line passes through a node or another label
- [ ] Marker ids unique per figure; no `<style>` inside any SVG
- [ ] Each `<svg>` has `role="img"` and an `aria-label` matching its caption's claim
- [ ] Both themes resolve; `.dg` inherits `color` so `currentColor` works in each
- [ ] Content verified against the code, not the docs
