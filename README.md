# drawing-workflow-diagrams

A Claude Code skill for turning "how does this actually work?" into a diagram somebody can read.

Ask Claude for a workflow diagram, an HLD, or an architecture map, and this skill decides the
layout, hand-authors the SVG, and publishes it as an Artifact page — instead of producing another
box-and-arrow sketch that restates the prose.

## What it gives Claude

- **A layout contract for swimlanes.** One lane per owner, two rows per lane, and a gutter kept
  empty for handoffs and failure loops. Broker and queue nodes straddle the lane divider, because
  that is what they are.
- **Encodings that mean something.** Solid is a service component, dashed is an external system,
  accent-filled is a datastore, amber-filled is a terminal outcome. Accent edges are the success
  path; amber edges are failure and retry. Colour is never decorative.
- **A palette and type pairing that already work** in light and dark, with all three theme states
  handled (explicit light, explicit dark, and the un-stamped system default most viewers see).
- **Eight geometry gotchas** that each cost a rewrite when missed — long connectors crossing nodes,
  marker ids colliding between figures, table chips placed where an arrow cannot reach them.
- **A pre-publish checklist**, because an unlabelled arrow means "related somehow" and nothing more.

## Install

```
/plugin marketplace add notsahill/drawing-workflow-diagrams
/plugin install drawing-workflow-diagrams@drawing-workflow-diagrams
```

Then just ask — "draw the workflow for this service", "diagram how a request flows through here",
"show me this as a state machine". The skill fires on its own.

Prefer not to use plugins? Copy `skills/drawing-workflow-diagrams/` into `~/.claude/skills/`.

## What's in the box

| File | Purpose |
|---|---|
| `SKILL.md` | When to fire, the style, layout recipe, geometry gotchas, page composition, checklist |
| `diagram-kit.css` | The full token and class kit — paste into the page's `<style>` |
| `lane-skeleton.svg` | A swimlane starter encoding the geometry contract |

## Make it yours

The shipped look is greenbar continuous-form paper with IBM Plex — it suits systems, data and
infrastructure subjects. Rebranding is three edits, all in `diagram-kit.css`:

1. Replace the ten colour values under bare `:root`, then the same token names in both dark blocks.
2. Swap the Google Fonts link and the `--sans` / `--serif` / `--mono` tokens.
3. Nothing else. The classes, geometry and encodings are palette-independent.

Two rules are worth keeping whatever colours you choose: accent means success, amber means failure,
and neutrals carry a slight hue bias toward your accent rather than sitting on pure grey.

## Requires

Claude Code with Artifacts enabled. The skill loads Anthropic's `artifact-design` and
`artifact-diagramming` skills as background before it writes anything.

## License

Apache-2.0
